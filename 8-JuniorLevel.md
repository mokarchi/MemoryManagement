What's the issue with this static field?

```csharp
public class Logger
{
    private static List<string> _logs = new List<string>();
    
    public void Log(string message)
    {
        _logs.Add(message);
    }
}
```
The issue with the static field _logs in your Logger class lies in its shared nature across all instances of the Logger class. This can lead to unintended consequences in multi-threaded or multi-instance scenarios. Here are the main problems:

1. Thread Safety:
The _logs field is a static field, which means it is shared across all instances of Logger. If multiple threads or instances of Logger are being used, calls to Log might cause race conditions. For example, multiple threads could try to add messages to _logs simultaneously, leading to data corruption or runtime exceptions.
Solution: Use a thread-safe collection or synchronize access to _logs. For example, you could use a ConcurrentBag<string> or lock around _logs operations.

2. Global State:
Since _logs is static, it acts as global state. This means that all instances of Logger share the same _logs list. This is likely not the intended behavior if you want each Logger instance to have its own log storage. It can lead to unexpected behaviors, such as logs from different instances being mixed together.
Solution: Remove the static keyword if the logs should be instance-specific.

3. Memory Leak Risks:
If _logs grows indefinitely, it could lead to a memory leak, as the List<string> will hold all log messages in memory until the application terminates.
Solution: Implement a mechanism to limit the size of _logs, such as removing old log entries after reaching a certain capacity.

# Suggested Fixes

If you want instance-specific logs:
```
public class Logger
{
    private List<string> _logs = new List<string>();
    
    public void Log(string message)
    {
        _logs.Add(message);
    }
}
```
If you want to maintain a shared log across all threads and instances:
```
public class Logger
{
    private static readonly object _lock = new object();
    private static List<string> _logs = new List<string>();
    
    public void Log(string message)
    {
        lock (_lock)
        {
            _logs.Add(message);
        }
    }
}
```
If you want thread-safety with a modern approach:
```
public class Logger
{
    private static readonly System.Collections.Concurrent.ConcurrentBag<string> _logs = new System.Collections.Concurrent.ConcurrentBag<string>();
    
    public void Log(string message)
    {
        _logs.Add(message);
    }
}
```
