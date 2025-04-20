What's wrong with this IDisposable implementation?

```csharp

public class ResourceHolder : IDisposable
{
    private Stream _stream = File.OpenRead("file.txt");
    
    public void Dispose()
    {
        _stream.Close();
    }
}
```
The ResourceHolder class has a potential issue in its Dispose method. While it closes the stream, it does not properly dispose of it. Here's the corrected implementation:
# Not following the Disposable pattern
This implementation cannot be extended properly.

If someone inherits from ResourceHolder and tries to override Dispose(), they have to use new Dispose() — not override — because the base method isn’t virtual.

That means if you do something like this:

```csharp
using (ResourceHolder holder = new DerivedResourceHolder())
{
    // Only base.Dispose() is called, not the derived one!
}
```
The derived class's cleanup logic will never be executed inside a using block — which leads to resource leaks or memory leaks.
# No support for unmanaged resources or finalization
There’s no Dispose(bool disposing) overload, so there's no way to separate managed and unmanaged cleanup.

There's no finalizer (~ResourceHolder()), meaning if someone forgets to call Dispose(), the stream may never be closed.
# Proper Design: The Disposable Pattern

To fix all of the above, we use the recommended pattern with Dispose(bool disposing) and GC.SuppressFinalize(this):
```csharp
public class ResourceHolder : IDisposable
{
    private Stream? _stream = File.OpenRead("file.txt");
    private bool _disposed = false;

    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this);
    }

    protected virtual void Dispose(bool disposing)
    {
        if (!_disposed)
        {
            if (disposing)
            {
                _stream?.Dispose();
            }

            _disposed = true;
        }
    }

    ~ResourceHolder()
    {
        Dispose(false);
    }
}
```
