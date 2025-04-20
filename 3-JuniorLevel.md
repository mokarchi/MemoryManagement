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
# 1. Strings in C# Are Immutable
In C#, strings are immutable, meaning that once a string is created, it cannot be changed. When you use the += operator to concatenate strings, a new string is created in memory every time, and the old string is discarded. This results in:
*	Excessive memory allocation: Each iteration creates a new string object, which increases memory usage.
*	Garbage collection overhead: The discarded strings need to be cleaned up by the garbage collector, which can slow down the application.
# 2. Time Complexity
The += operator in a loop has a quadratic time complexity (O(n^2)) because:
*	In each iteration, the entire string is copied to create a new one.
*	As the string grows longer, the copying process takes more time.
For example:
*	In the first iteration, result is "0".
*	In the second iteration, result becomes "01", requiring the previous string "0" to be copied.
*	By the 10,000th iteration, the string has grown significantly, and copying it becomes increasingly expensive.
# 3. Better Alternative: StringBuilder
To address these issues, you should use the StringBuilder class, which is specifically designed for efficient string manipulation. StringBuilder maintains a mutable buffer, avoiding the need to create new string objects for each concatenation.
Optimized Code Using StringBuilder
```csharp
using System.Text;

public string BuildString()
{
    StringBuilder result = new StringBuilder();
    for (int i = 0; i < 10000; i++)
    {
        result.Append(i.ToString());
    }
    return result.ToString();
}
```
# Why StringBuilder Is Better
1.	Efficient Memory Usage: StringBuilder allocates a buffer that can grow as needed, reducing the number of memory allocations.
2.	Improved Performance: The time complexity is closer to O(n) because it avoids repeatedly copying the entire string.
3.	Scalability: It handles large-scale string concatenation tasks more effectively.
# When to Use StringBuilder
*	Use StringBuilder when concatenating strings in a loop or when working with a large number of string operations.
*	For small-scale concatenations (e.g., a few strings), the + or += operator is fine because the performance difference is negligible.
