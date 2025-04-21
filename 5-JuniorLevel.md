What's the issue with this string concatenation code?

```csharp

public string BuildString()
{
    string result = "";
    for(int i = 0; i < 10000; i++)
    {
        result += i.ToString();
    }
    return result;
}
```
The issue with this C# code lies in its performance, specifically how it handles string concatenation within the loop.

1. String Immutability: In C#, string objects are immutable. This means that every time you use the += operator on a string (result += i.ToString();), you are not modifying the existing result string. Instead, a new string object is created in memory that combines the old result and the new i.ToString(). The previous result object is then discarded and eventually garbage collected.
2. Inefficiency in Loops: Doing this repeatedly in a loop (10,000 times in this case) is very inefficient. It leads to:
  * High Memory Allocation: Many intermediate string objects are created and quickly discarded, putting pressure on the garbage collector.
  * Poor Performance: The process of allocating memory for new strings and copying the contents from the old ones takes significant time, especially as the result string grows larger.

# The Recommended Solution:

Use the System.Text.StringBuilder class for building strings iteratively. StringBuilder is designed for scenarios like this; it manages an internal buffer and appends data efficiently without creating new objects on each append operation.

Here's how you would rewrite the code using StringBuilder:
```csharp
using System.Text; // Required for StringBuilder

public string BuildStringEfficiently()
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < 10000; i++)
    {
        sb.Append(i.ToString()); // Use Append for efficiency
    }
    return sb.ToString(); // Convert to a final string only once
}
```
