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


// Define a namespace — a way to logically group related code.
// Helps organize code and avoid name conflicts.
namespace HelloWorld
{
    // Define a class named 'Program'.
    // In C#, all code must live inside a class or struct.
    class Program
    {
        // This is the entry point of the program.
        // 'static' means it belongs to the class itself, not an object instance.
        // 'void' means it returns nothing.
        // 'string[] args' allows passing command-line arguments.
        static void Main(string[] args)
        {
            // This line writes text to the console (standard output).
            // 'Console' is a class in the System namespace.
            Console.WriteLine("Hello, world!");
        }
    }
}
```

**To execute the app, press ctrl + G5.**
