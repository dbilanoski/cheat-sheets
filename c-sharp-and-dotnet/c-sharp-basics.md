# C# Notes

C and C++ were compiled languages compiling directly to the machine code. This meant different compiling for different operating systems.

C# introduced compiling to IL (intermediate language code), same as Java, easing the cross-platform compatibility.
- Here, the CLR (Common Language Run-time) translates the IL code to the machine code (process called JIT - just-in-time compiling).

While C# is a programming language, .NET is a framework for building apps in Windows.
* Different languages can target\use .NET framework

https://www.youtube.com/watch?v=GhQdlIFylQ8 3:55:04
## Syntax Specifics

* Case sensitive
* Uses curly braces for namespaces and classes ({}).
* Statements need to be terminated with semicolons (;).
* Supports explicit and implicit data type declarations.
* When naming variables (identifiers):
	* Cannot start with number.
	* Cannot have empty spaces.
	* Cannot use reserved keywords (`int` for example).
	* Naming conventions: camelCase, PascalCase.

## Data Types

### Variables

**Variable:** a name given to a storage location in memory
- Can be initialized without a value but program cannot be compiled or variable used if not assigned a value (cannot stay empty).
- Data type can be specified explicitly or not, by using `var` keyword leaving C# to determine the type.
- Use Camel Case for identifiers.

**Constant:** an immutable value (cannot be changed once declared)
- Cannot be initialized empty.
- Data type must be declared expicitly.
- Use Pascal Case for identifiers.
  
```c#
// Constants
const double Pi = 3.14159;     // Cannot change later
readonly int userId = 1234;    // Can only be set once in constructor

// Explicit typing
byte num = 255;
int count = 22;
float num2 = 33.4F; // needs an F suffix to be treated as float
double price = 9.99;
char character = 'a'; // Characters are enclosed with single qoutes
string firstName = "Danilo"; // Strings are enclosed with double quotes
bool isWorking = false;

// Implicit typing (type is inferred from value)
var dynamicType = true; // var keywork let's c# decide the datatype

// Print them out each on new line
Console.WriteLine(num + "\n" + count + "\n" + num2 + "\n" + character + "\n" + firstName + "\n" + isWorking + "\n" + dynamicType);
```

### Primitive Data Types

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

If range is exceeded, overflow happens.

```c#
// Overflows
byte number = 255;
number += 1;
Console.WriteLine(number); // Outputs 0 because byte cannot be larger than 255, which is why it overflows

// To handle it, "checked" statement can be used. Inside it, it will throw an exception
checked
{
    byte number = 255;
    number += 1;
    Console.WriteLine(number); // exception is thrown 
}
            
```

Tips:
- Use `int` by default for whole numbers.
- Use `double` for general-purpose decimals, `decimal` for money.
- Use `string` for text, `bool` for yes/no logic.
- Use `char` when you only need a single character, not a whole string.

#### Type Casting

Can be implicit, which is done automatically (safer as no data loss can occur) and explicit where you must specify the type (can result  with data loss if type does not support range of values you are trying to convert.)

If working with incompatible types, use `Convert` methods for conversion.

```c#
// 1. IMPLICIT CONVERSION
int a = 100;
double b = a;  // Implicit conversion: int → double

char letter = 'A';
int code = letter; // char → int (Unicode value of 'A' = 65)

//2. EXPLICIT CONVERSION
double pi = 3.14159;
int whole = (int)pi;  // whole = 3, fractional part is lost

int bigNumber = 300;
byte small = (byte)bigNumber; // small = 44 (data loss! overflows 255 limit)

// 3. Using Conversion Methods (Safe Alternatives)
string input = "123";
int number = Convert.ToInt32(input); // Throws exception if invalid

double d = 9.87;
string text = d.ToString(); // Converts to "9.87"


// 4. Using TryParse (Safe Conversion from String)
string input = "456";
if (int.TryParse(input, out int result))
{
    Console.WriteLine($"Converted: {result}");
}
else
{
    Console.WriteLine("Invalid number");
}
```

### Non-primitive Data Types / Data Structures

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

#### Arrays

- Arrays have fixed size, cannot resize them after creation.
- Index out of bounds will throw a runtime error.

```c#
int [] numbers = { 1, 2, 3 };
string [] allowedOperators = { "+", "-", "/", "*" };

// Blank array with 5 items, later adding one by one
int blankArray = new string[5];
blankArray[0] = 2;
blankArray[1] = 3;

// Accessing values
Console.WriteLine(numbers[0]); // 1

// Updating values
numbers[1] = 10;

/*
Array Methods
*/
int length = numbers.Length; // Lenght
Array.Sort(numbers); // Sort
Array.Reverse(numbers); // Reverse

```

#### Lists

* Resizable arrays, can be expanded after creation.
* Accessing an index that doesn’t exist throws an exception.

```c#
// Empty list
List<string> fruits = new List<string>(); 

// List initialized with values
List<int> primes = new List<int> { 2, 3, 5 };

// Adding values
fruits.Add("Apple");
fruits.AddRange(new[] { "Banana", "Cherry" });

// Removing values
fruits.Remove("Apple");        // Removes by value, removes only first mathc.
fruits.RemoveAt(0);            // Removes by index
fruits.Clear();                // Removes all items

// Accessing values
string first = fruits[0];

// Updating values
fruits[1] = "Blueberry";

// Useful methods
fruits.Contains("Banana");
fruits.IndexOf("Cherry");
fruits.Sort();
fruits.Reverse();
fruits.Count;                  // Number of items
```

#### Dictionaries

- Key-value pairs, keys must be unique.

```c#
// Explicit declaration with no values
Dictionary<string, int> ages = new Dictionary<string, int>();

// Implicit declaration with no values
var ages = new Dictionary<string, int>();

// Implicit declaration with values
var capitals = new Dictionary<string, string>
{
    { "USA", "Washington" },
    { "France", "Paris" }
};

/* Adding / Updating / Removing values */
ages.Add("Alice", 30);
ages["Bob"] = 25; // Add or update
ages.Remove("Alice");
ages.Clear(); 

/* Accessing values */
int bobAge = ages["Bob"]; // Throws if "Bob" not found

// Safer:
if (ages.TryGetValue("Bob", out int age))
{
    Console.WriteLine($"Bob is {age}");
}

/* Useful Methods */
ages.ContainsKey("Bob");
ages.ContainsValue(30);
ages.Count;
```

### Classes

- Custom data types for complex constructs.
- A blueprint for creating **objects**. It bundles **data** (fields/properties) and **behavior** (methods) together.
- Items in a class can be **static** or **instance** members - static members do not need an instance of the object to be executed while instance members need it.

```c#
// This is a class definition
public class Person
{
    // Field: a variable tied to this class (not recommended to keep public)
    private string name;
    
    // Property: preferred way to expose data (encapsulates access)
    public string Name
    {
        get { return name; }
        set { name = value; }
    }
	
	 // Static member (attribute) (can be called without instantiating object first)
    public static string species = "Human"; 
	
	// Static member (method) (can be called without instantiating object first)
    public static void SayHi(string name)
    {
	    Console.WriteLine($"Howdy, {name}!")
    } 

    // Constructor: special method that runs when you create a new object
    public Person(string personName)
    {
        name = personName;
    }

    // Method: defines behavior
    public void Greet()
    {
        Console.WriteLine($"Hello, my name is {name}.");
    }
}

// Usage
class Program
{
    static void Main()
    {
        Person p = new Person("Alice");  // Create an object
        p.Greet();                        // Call method
        p.Name = "Bob";                   // Use property to change name
        Console.WriteLine(p.Name);        // Get the name
        Console.WriteLine(Person.species) // Call the static attribute (does not need an instance of the object)
        Person.SayHi("Dan") // Call the static method (does not need instance of the object)
    }
}
```

#### Access Modifiers

Keywords (private, internal, etc.) which control who can access what.

| Modifier             | Where accessible from                | Use case                                          |
| -------------------- | ------------------------------------ | ------------------------------------------------- |
| `public`             | Anywhere (outside of the class code) | Use to expose members or classes to all           |
| `private`            | Only inside the same class           | Default for fields; use for encapsulation         |
| `internal`           | Only in the same project/assembly    | Hides from other projects, but visible internally |
| `protected`          | In this class and derived classes    | Use for inheritance scenarios                     |
| `protected internal` | Same assembly + derived types        | Rare, but valid hybrid case                       |
| `private protected`  | Derived types in same assembly       | Narrower inheritance case                         |


#### Fields VS Properties (Getters And Setters)

Field is a variable inside class, while property is a controlled access to the field usually achieved by using getters and setters.

```c#
/*
Public field 
	- Available outside of class code, breaks encapsulation-
	- Can be accessed normally when the object is initiated both for getting and setting values.
	- Does not require getters and setters.
*/
public string name; 

/*
Controlled access
	- First we define private property (avialable only to the class code).
	- Cannot be accessed normally when the object is initiated.
	- Needs special method to define getting and setting logic.
	- Convention is to have property lowercased, then the getter/setter method with same name but capitalized.
*/
private string name;

public string Name
{
    get { return name; }
    set 
    { 
	    // Here we're getting value from the user and assigning it to our property
	    // Custom control logic can go here
	    name = value; 
	}
}
```



#### Constructors

- Special method that runs when you create a new object.
- We create it using same name as the class.

```c#
public class Dog
{
    public string Name { get; set; }

    // Constructor
    public Dog(string name)
    {
        Name = name;
    }
}


// When used, object creation will requrire a string "name" which will update the property Name
Dog myDog = new Dog("Good Boy");
```

#### Methods

```c#
public void Bark()
{
    Console.WriteLine($"{Name} says Woof!");
}

// Method with return
public int GetNameLength()
{
    return Name.Length;
}
```

## Operators

| **Category**         | **Operator**           | **Description**                          | **Example**                    |     |
| -------------------- | ---------------------- | ---------------------------------------- | ------------------------------ | --- |
| **Arithmetic**       | `+`                    | Addition                                 | `5 + 2` → `7`                  |     |
|                      | `-`                    | Subtraction                              | `5 - 2` → `3`                  |     |
|                      | `*`                    | Multiplication                           | `5 * 2` → `10`                 |     |
|                      | `/`                    | Division (integer or floating-point)     | `5 / 2` → `2` (`int` division) |     |
|                      | `%`                    | Modulus (remainder)                      | `5 % 2` → `1`                  |     |
| **Unary**            | `+` / `-`              | Unary plus/minus                         | `-a`                           |     |
|                      | `++` / `--`            | Increment / Decrement                    | `a++`, `--b`                   |     |
| **Assignment**       | `=`                    | Assignment                               | `x = 5`                        |     |
|                      | `+=`, `-=`, `*=`, etc. | Compound assignment                      | `x += 2` → `x = x + 2`         |     |
| **Comparison**       | `==`                   | Equal to                                 | `x == y`                       |     |
|                      | `!=`                   | Not equal to                             | `x != y`                       |     |
|                      | `>` / `<`              | Greater / Less than                      | `x > y`, `x < y`               |     |
|                      | `>=` / `<=`            | Greater / Less than or equal             | `x >= y`, `x <= y`             |     |
| **Logical**          | `&&`                   | Logical AND                              | `x > 0 && y > 0`               |     |
|                      | <code>\|\|</code>      | Logical OR                               | <code>a \|\| b</code>          |     |
|                      | `!`                    | Logical NOT                              | `!isActive`                    |     |
| **Bitwise**          | `&`                    | Bitwise AND                              | `a & b`                        |     |
|                      | <code>\|</code>        | Bitwise OR                               | <code>a \| b</code>            |     |
|                      | `^`                    | Bitwise XOR                              | `a ^ b`                        |     |
|                      | `~`                    | Bitwise NOT                              | `~a`                           |     |
|                      | `<<` / `>>`            | Left / Right shift                       | `a << 1`, `b >> 2`             |     |
| **Null-coalescing**  | `??`                   | Returns left if not null, else right     | `name ?? "Default"`            |     |
|                      | `??=`                  | Assign right if left is null             | `name ??= "Guest"`             |     |
| **Ternary**          | `?:`                   | Conditional operator (if/else shorthand) | `x > 0 ? "Yes" : "No"`         |     |
| **Type check**       | `is`                   | Check type                               | `obj is string`                |     |
|                      | `as`                   | Safe cast (null if fails)                | `obj as string`                |     |
| **Null-conditional** | `?.`                   | Calls member only if not null            | `user?.Name`                   |     |
| **Lambda**           | `=>`                   | Lambda expression (arrow function)       | `(x, y) => x + y`              |     |
|                      |                        |                                          |                                |     |
## Flow Control

### If Statement

```c#
int age = 18;

if (age >= 18)
{
    Console.WriteLine("You're an adult.");
}
else if (age >= 13)
{
    Console.WriteLine("You're a teenager.");
}
else
{
    Console.WriteLine("You're a child.");
}
```

### Switch Statement

- Always include `default` to handle unexpected cases.
- Don't forget `break;` or execution will "fall through" to next case (unless intentional)

```c#
string grade = "B";

switch (grade)
{
    case "A":
        Console.WriteLine("Excellent!");
        break;
    case "B":
    case "C":
        Console.WriteLine("Good job.");
        break;
    case "D":
        Console.WriteLine("You passed.");
        break;
    case "F":
        Console.WriteLine("Try again.");
        break;
    default:
        Console.WriteLine("Invalid grade.");
        break;
}
```

### For Loop

- Useful when you need index-based access or known iteration count.
- Changing the collection you're iterating over inside the loop can cause runtime errors.
- 
```c#
for (int i = 0; i < 5; i++)
{
    Console.WriteLine($"i = {i}");
}

```

### While Loop

- Make sure to terminate loop to avoid infinite loops.

```c#
int number = 0;

while (number < 3)
{
    Console.WriteLine($"Number is {number}");
    // Increment control variable
    number++;
}
```

### Do...While Loop

- This type of look executes the code block before checking the condition, essentially guaranteeing at leas one execution of the loop.
- Can cause infinite loops if the condition never becomes false.

```c#
do
{
    Console.Write("Enter a number greater than 0: ");
    input = int.Parse(Console.ReadLine());
}
while (input <= 0);
```

### Foreach Loop

- Use `foreach` when you don't need to modify the collection or use indices.
- You **cannot modify** the collection (remove items) during iteration.

```c#
string[] fruits = { "Apple", "Banana", "Cherry" };

foreach (string fruit in fruits)
{
    Console.WriteLine(fruit);
}

```

### Break & Continue Statements

**Use `break;` to exit the loop early.**

```c#
for (int i = 0; i < 10; i++)
{
    if (i == 5)
        break;  // Exits the loop when i == 5

    Console.WriteLine(i);
}
```

**Use `continue` to skip current iteration.**

```c#
for (int i = 0; i < 5; i++)
{
    if (i == 2)
        continue;  // Skip the rest of the loop body for i == 2

    Console.WriteLine(i);
}
```


### GOTO Statement

- Jumps to a labeled statement.
- `goto` makes code harder to read and maintain. Best avoided unless truly needed (like breaking nested loops).
```c#
int counter = 0;

start:
Console.WriteLine(counter);
counter++;
if (counter < 3)
    goto start;
```

#### Example Where GOTO Makes Sense

Example of searching a value in a 2D array:
- Without `goto`, you'd have to use a flag and `break` each loop manually, or wrap the loops in a method.
- It's readable and direct in this small localized context.
- This pattern is still used in some real-world codebases when nested loops are hard to refactor or performance-sensitive.

```c#
// Define 2d array
int[,] matrix = {
    { 1, 2, 3 },
    { 4, 5, 6 },
    { 7, 8, 9 }
};

// Define control flags
int target = 5;
bool found = false;


// Iterate over rows
for (int row = 0; row < matrix.GetLength(0); row++)
{
    // In each row, iterate over cols
    for (int col = 0; col < matrix.GetLength(1); col++)
    {
        // If match is found, use 'goto' to break out of both loops
        if (matrix[row, col] == target)
        {
            Console.WriteLine($"Found {target} at position [{row}, {col}]");
            // Update flag
            found = true;
            // Jump out of both loops
            goto DoneSearching; 
        }
    }
}
// Label that marks where to jump to
DoneSearching:
if (!found)
{
    Console.WriteLine($"{target} was not found in the matrix.");
}
```

## Error Handling


```c#
// Try-catch block example

try
{
	string s = "1234";
	byte b = Convert.ToByte(s);
	Console.WriteLine(s);
}
catch (Exception e)
{
	Console.WriteLine($"The number could not be converted to byte, error: {e.Message}");
}
finally
{
	Console.WriteLine("This runs regardles.");
}
```


## Methods
- Naming convention is camel case.

```c#
using System;
using System.Runtime.InteropServices;
using System.Text.Json;
using System.Threading.Tasks.Dataflow;

namespace ExampleApp 
{
	class Program {
		static void Main()
		{
			// Main function, executes by default
			// To run methods defined below, here it's called
			SayHowdy("Danilo");
			Console.WriteLine(Cube(3)); 
		}
		
		static void SayHowdy(string name="User")
		{
			// Greets user
			Console.WriteLine($"Howdy, {name}!");
		}
			 
		static double Cube(double number)
		{
			 // Returns cubed number
			 double result = Math.Pow(number, 3);
			 return result;
        }
	
	}
}
```