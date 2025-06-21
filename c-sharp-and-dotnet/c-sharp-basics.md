## Basics
https://www.youtube.com/watch?v=gfkTfcpWqAY
27.24

C and C++ were compiled languages compiling directly to the machine code. This meant different compiling for different operating systems.

C# introduced compiling to IL (intermediate language code), same as Java, easing the cross-platform compatibility.
- Here, the CLR (Common Language Run-time) translates the IL code to the machine code (process called JIT - just-in-time compiling).

While C# is a programming language, .NET is a framework for building apps in Windows.
* Different languages can target\use .NET framework


### Syntax Specifics

* Case sensitive
* Uses curly braces for namespaces and classes ({}).
* Statements need to be terminated with semicolons (;).
* Type needs to be specified when declaring variables.
* When naming variables (identifiers):
	* Cannot start with number.
	* Cannot have empty spaces.
	* Cannot use reserved keywords (`int` for example).
	* Naming conventions: camelCase, PascalCase.

### Data Types

#### Variables

**Variable:** a name given to a storage location in memory
- Can be initialized without a value but program cannot be compiled or variable used if not assigned a value (cannot stay empty).
- Use Camel Case for identifiers.

**Constant:** an immutable value (cannot be changed once declared)
- Cannot be initialized empty.
- Use Pascal Case for identifiers.
  
```c#
// Types need to be specified when declaring variables
int number;
int Number = 1;
const float Pi = 3.16f;
```

#### Primitive Data Types

Each C# type maps to a .NET type which will be translated on compiling.

| C# Type   | .NET Type        | Bytes  | Range                                       | Description                                     |
| --------- | ---------------- | ------ | ------------------------------------------- | ----------------------------------------------- |
| `byte`    | `System.Byte`    | 1      | 0 to 255                                    | Unsigned 8-bit integer                          |
| `sbyte`   | `System.SByte`   | 1      | –128 to 127                                 | Signed 8-bit integer                            |
| `short`   | `System.Int16`   | 2      | –32,768 to 32,767                           | Signed 16-bit integer                           |
| `ushort`  | `System.UInt16`  | 2      | 0 to 65,535                                 | Unsigned 16-bit integer                         |
| `int`     | `System.Int32`   | 4      | –2.1B to 2.1B                               | Commonly used signed 32-bit integer             |
| `uint`    | `System.UInt32`  | 4      | 0 to 4.2B                                   | Unsigned 32-bit integer                         |
| `long`    | `System.Int64`   | 8      | ±9.2 quintillion                            | Signed 64-bit integer for large numbers         |
| `ulong`   | `System.UInt64`  | 8      | 0 to 18.4 quintillion                       | Unsigned 64-bit integer                         |
| `float`   | `System.Single`  | 4      | ~±1.5×10⁻⁴⁵ to ±3.4×10³⁸                    | 32-bit floating point (less precise)            |
| `double`  | `System.Double`  | 8      | ~±5.0×10⁻³²⁴ to ±1.7×10³⁰⁸                  | 64-bit floating point (more precise)            |
| `decimal` | `System.Decimal` | 16     | ±1.0×10⁻²⁸ to ±7.9×10²⁸                     | High-precision for financial/money calculations |
| `char`    | `System.Char`    | 2      | Single Unicode character (U+0000 to U+FFFF) | Represents a single character                   |
| `bool`    | `System.Boolean` | 1      | `true` or `false`                           | Boolean logic type                              |
| `string`  | `System.String`  | Varies | N/A                                         | A sequence of characters (text)                 |
| `object`  | `System.Object`  | Varies | N/A                                         | Base type for all C# types (value or reference) |

Tips:
- Use `int` by default for whole numbers.
- Use `double` for general-purpose decimals, `decimal` for money.
- Use `string` for text, `bool` for yes/no logic.
- Use `char` when you only need a single character, not a whole string.


#### Non-primitive Data Types

| Type                       | .NET Type                                             | Category              | Description                                                    |
| -------------------------- | ----------------------------------------------------- | --------------------- | -------------------------------------------------------------- |
| `string`                   | `System.String`                                       | Immutable object      | Represents text (sequence of `char`); behaves like a primitive |
| `object`                   | `System.Object`                                       | Root type             | Base type for all C# types                                     |
| `dynamic`                  | `System.Object`                                       | Dynamic type          | Allows late binding (type resolved at runtime)                 |
| `List<T>`                  | `System.Collections.Generic.List<T>`                  | Generic collection    | Resizable, ordered list of items                               |
| `Dictionary<TKey, TValue>` | `System.Collections.Generic.Dictionary<TKey, TValue>` | Key-value store       | Efficient lookup table using unique keys                       |
| `Array`                    | `System.Array`                                        | Fixed-size collection | Stores a fixed-length sequence of elements                     |
| `DateTime`                 | `System.DateTime`                                     | Struct (value)        | Represents date and time                                       |
| `TimeSpan`                 | `System.TimeSpan`                                     | Struct (value)        | Represents time intervals                                      |
| `Stream`                   | `System.IO.Stream`                                    | Base class            | Abstract base for file/network/memory I/O streams              |
| `FileStream`               | `System.IO.FileStream`                                | Derived class         | Reads/writes files as a stream                                 |
| `Exception`                | `System.Exception`                                    | Base class            | Base class for errors/exceptions                               |
| `Task`                     | `System.Threading.Tasks.Task`                         | Async type            | Represents an asynchronous operation                           |
| `Guid`                     | `System.Guid`                                         | Struct (value)        | Globally unique identifier                                     |
| `Tuple<T1, T2>`            | `System.Tuple<T1, T2>`                                | Utility type          | Groups multiple values without defining a new class            |
| `Record`                   | `N/A` (C# keyword)                                    | Immutable type        | Immutable data container introduced in C# 9                    |
| `class` / `struct`         | User-defined                                          | Custom type           | Blueprint for objects (`class` = ref type, `struct` = value)   |

## Architecture of .NET apps

Consists of classes, containing data and methods.

To organize classes,  **namespaces** are used, as containers of related classes.

Then as the application grows **assemblies** are used, as containers for related namespaces.
- It's a file on disk and can by an executable (EXE) or dynamic library (DLL).

### Application Structure & Example

```text
MyDotNetApp/
│
├── Program.cs
│   - The entry point of the application.
|── App.config 
|   - Xml file holding application related settings, such as db connections trings.
|   - Used in older, .NET Framework 4.8 projects.
|   - appsettigns.json is used on modern .NET (5 +).
│── Properties
|   |── AssemblyInfo.cs
|       - Information to be used on compiling (title, decription, etc.).
|── References
|	|── System
|   |── System.core
|   |── etc.
|       - Any assemblies the application is referencing (imports).
|		- Visual Studio virtual folder (not a real folder on disk).
```

`Program.cs` example

```c#
// Imports
/*
Keyword using imports namespaces so classes are available in this namespace.
Basic .NET namespaces are added automatically.
*/
using System; // Basic utility classes and primitive types
using System.Collections.Generic; // Data structures
using System.Linq; // Data
using System.Text; // Text related
using System.Threading.Taks // Parallelization

// Declare namespace for our application
namespace HelloWorld
{
	// Declare class for our program - by default its Program for console apps.
	class Program
	{
		// Main method of our program which get's executed automatically
		// This one takes an argument "args" which is type of string array
		// void means it returns nothing
		static void Main(string[] args)
		{
			// We using class called Console from the System namespace here
			// and it's methond Write.Line to write to console.
			Console.WriteLine("Hello World");
		}
	}
}
```

**To execute the app, press ctrl + G5.**

