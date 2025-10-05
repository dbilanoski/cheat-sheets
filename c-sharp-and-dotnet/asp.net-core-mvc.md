# ASP-NET Core Model View Controller Notes


https://www.youtube.com/watch?v=RWXKysImabs
26:08

**ASP.NET MVC** is a web application framework by Microsoft that follows the **Model-View-Controller (MVC)** pattern. It helps building web apps with separation of concerns, which means the app is organized and maintainable.

## Model-View-Controller Pattern

Clear division of responsibilities in application allowing for easier development, maintenance and debugging. Features three areas - Model, View and Controller. 

### 1. Model

- Represents data and handles logic for processing data.
- Often interacts with a **database**.
- Example: `Product.cs` represents a product entity.
  
  ```csharp
	public class Product {
	    public int Id { get; set; }
	    public string Name { get; set; }
	    public decimal Price { get; set; }
	}
	```

### 2. View

- UI part, usually HTML + Razor.
- Displays data to user.
- Example: `Index.cshtml` showing the list of products.
  
  ```html
	@model List<Product>

	<h2>Product List</h2>
	<ul>
	@foreach (var product in Model) {
	    <li>@product.Name - $@product.Price</li>
	}
	</ul>
	```


### 3. Controller

- Handles user input and interactions.
- Handles business logic.
- Determines which Model to use and sends data to views, basically acting as a central logic of the app deciding which Model to use and which View to render.
  
  ```csharp
	public class ProductController : Controller {
		public ActionResult Index() {
			var products = new List<Product> {
				new Product { Id = 1, Name = "Phone", Price = 699 },
				new Product { Id = 2, Name = "Laptop", Price = 1299 }
			};
	
			return View(products);
			}
	}
	```


## ASP.NET Core MVC Application Basics

### Application Structure


```text
MyApp/
├── Properties/
|   └── launchSettings.json
├── Controllers/
│   └── HomeController.cs
├── Models/
│   └── User.cs
├── Views/
│   ├── Home/
│   │   └── Index.cshtml
│   └── Shared/
│       ├── _Layout.cshtml
│       └── _ValidationScriptsPartial.cshtml
├── wwwroot/
│   ├── css/
│   ├── js/
│   └── lib/
├── appsettings.json
├── Program.cs
├── Startup.cs (only for older versions < .NET 6)
├── MyApp.csproj
```

**Important folders:**
- Properties:
	- Contains `launchSettings.json` which is used to control environment variables, profiles settings and applicaiton URLs.
	- Super useful during development.
- Controllers:  
	- Contains the controller classes than handle incoming HTTP requests. 
	- They handle user input, interact with the model and return views.
	- Example: `HomeController.cs` might have an action method like `public IActionResult Index()` that returns the Home/Index view.
- Models:
	- Contains the application's data structures and business logic around the data.
	- Example: `User.cs` might define properties like `Name`, `Email`, etc., and may be annotated with validation attributes.
- Views:
	- Contains `.cshtml` documents (Razor views) which are used to render HTML to the browser.
	- There will be a subfolder per controller and one for shared resources.
- wwwroot
	- Public, static web assets like CSS, JavaScript and images.
	- Will have subfolders for different parts (css, js, lib for libraries etc.).

**Important files:**
- appsettings.json_
	- Configuration file for the application.
	- Connection strings, logging levels, etc.
- Program.cs and Startup.cs_
	- Entry point of the application.
	- Configures the web host, services, middleware pipline and routing.
	- Before .NET +, Startup.cs was used for startup logic but now only Program.cs is needed.
- MyApp.csproj: project file that defines dependencies, SDK version, build settings etc.


### Application Flow & Logic

When user enters URL, here's the high level flow:

- URL → Program entry point (`Program.cs`,  sets up routing)
- Route → Maps to controller/action
- Controller → Calls a service
- Service → Returns a model
- Controller → Passes model to the view
- View → Renders final HTML


Detailed Steps

1. Browser sends a GET requtest to `https://localhost:5001/Home/Details/5`
2. Routing defined in `Program.cs` or `Startup.cs` matches the URL pattern:
   
   ```csharp
    endpoints.MapControllerRoute(
	    name: "default",
	    pattern: "{controller=Home}/{action=Index}/{id?}"
	    );
	```
	
3. Route pattern resolves to:
       - `controller = "Home"`
       - `action = "Details"`
       - `id = 5`
4. ASP.NET Core looks in the `Controllers/` folder for a file named: `HomeController.cs` and loads the `HomeController` class from it.
5. ASP.NET Core finds the action method `public IActionResult Details(int id)` from the `HomeController.cs` file and invokes it.
		- `IActionResult` is an interface ensuring appropriate return type for the action method, ensuring suitable result for the HTTP response..
		- The `id` parameter is automatically bound from the URL.

6. Inside the action method, controller calls a service from the `Models/` to apply needed data processing logic.
   - The service (`UserService`) contains business or data logic, and returns a `User` model.
   - This avoids putting logic directly in the controller.
     
   ```csharp
	var user = _userService.GetUserById(id);
	```   
	
7. `UserService` loads, instantiates and returns a `User` model, ready to be handed over to the view component for rendering.
     
   ```csharp
	public class UserService
	    {
	        public User GetUserById(int id)
	        {
	            // Simulate a user from a database
	            return new User
	            {
	                Id = id,
	                Name = "John Doe"
	            };
	        }
	    }
	```
	Inside that `UserService`, `User` model was loaded from the `Models/` directory which was handling the data returned to the service.
   ```csharp
	 public class User
	    {
	        public int Id { get; set; }
	        public string Name { get; set; }
	    }
	```
8. Controller returns the a `View` containing `User` model created by the `UserService` service passing it there for rendering.
   - This follows the "fat model, thin controller" pattern.
     
   ```csharp
    public IActionResult Details(int id)
	    {
	        var user = _userService.GetUserById(id); // Get data from model
	        return View(user); // Pass model to the view
	    }
	```
	
6. View called `Details.cshtml` uses Razor syntax to show the model data, after which the rendered HTML is sent to browser as a response.
   
   ```html
	<h1>@Model.Name</h1>
	```

### Code Example

Here are contents of the files showing full code of the example discussed above.

1. `Program.cs`
   
   ```csharp
	using Microsoft.AspNetCore.Builder;                 // For building the web app
	using Microsoft.Extensions.DependencyInjection;     // For adding services like MVC and DI
	using Microsoft.Extensions.Hosting;                 // For checking environment
	using MyApp.Models;                                 // Reference to our service and model classes
	
	var builder = WebApplication.CreateBuilder(args);   // Create the web app builder
	
	// Add MVC services (controllers + views) to the DI container
	builder.Services.AddControllersWithViews();
	
	// Register our custom service (UserService) for Dependency Injection
	builder.Services.AddScoped<UserService>();
	
	var app = builder.Build();                          // Build the app pipeline
	
	// If not in development mode, use a default error handler
	if (!app.Environment.IsDevelopment())
	{
	    app.UseExceptionHandler("/Home/Error");
	}
	
	app.UseStaticFiles();       // Allow serving static files like CSS, JS, images
	
	app.UseRouting();           // Enable routing for controllers
	
	app.UseAuthorization();     // (Required even if not using authentication)
	
	app.MapControllerRoute(     // Define the default route pattern
	    name: "default",
	    pattern: "{controller=Home}/{action=Index}/{id?}");
	
	app.Run();                  // Start the web application

	```
	
2. `Models/User.cs`
   
   ```csharp
   namespace MyApp.Models
	{
	    // This class represents the structure of a User
	    public class User
	    {
	        public int Id { get; set; }      // User's unique identifier
	        public string Name { get; set; } // User's name
	    }
	}

	```
	
3. `Models/UserService.cs`
   ```csharp
   namespace MyApp.Models
	{
	    // This class handles logic related to users
	    public class UserService
	    {
	        // Returns a User object based on an ID
	        public User GetUserById(int id)
	        {
	            // For now, just return a hard-coded user
	            return new User
	            {
	                Id = id,
	                Name = "John Doe"
	            };
	        }
	    }
	}
	```
	
4.  `Controllers/HomeController.cs`
   
   ```csharp
	using Microsoft.AspNetCore.Mvc;       // Base namespace for MVC features
	using MyApp.Models;                   // So we can use User and UserService

	namespace MyApp.Controllers
	{
	    // This is our controller for handling requests to /Home
	    public class HomeController : Controller
	    {
	        private readonly UserService _userService; // Field to store injected service
	
	        // Constructor with UserService injected by DI
	        public HomeController(UserService userService)
	        {
	            _userService = userService; // Store the service for use in actions
	        }
	
	        // This method handles requests to /Home/Details/{id}
	        public IActionResult Details(int id)
	        {
	            // Get the user data using our service
	            var user = _userService.GetUserById(id);
	
	            // Pass the model (user) to the view for rendering
	            return View(user);
	        }
	    }
	}
	```
	
5. `Views/Home/Details.cshtml`
   
   ```c#
   @model MyApp.Models.User  <!-- This view expects a User model -->

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="utf-8" />
	    <title>User Details</title> <!-- Page title shown in browser tab -->
	</head>
	<body>
	    <h1>User Details</h1>
	
	    <!-- Display the ID from the model -->
	    <p><strong>ID:</strong> @Model.Id</p>
	
	    <!-- Display the name from the model -->
	    <p><strong>Name:</strong> @Model.Name</p>
	</body>
	</html>

	```

**Result:** What Happens When You Visit `/Home/Details/1`

1. **Routing** matches `/Home/Details/1` → `HomeController.Details(1)`
2. **Controller** calls `UserService.GetUserById(1)`
3. **UserService** returns a `User` object
4. **Controller** passes `User` to the Razor **view**
5. **View** renders the `User` info as HTML
6. **Browser** displays:
   
	```text
	User Details
	ID: 1
	Name: John Doe
	```


### Core Components
#### `IActionResult` And Action Return Types

`IActionResult` is an interface specifying possible return types for the action method.

Controller can return different results referenced by type (`ViewResult`, `ContentResult`, etc.) but using an `IActionResult` provides option to select them all, so controller can utilize a logic returning different types depending on some condition.

* ViewResult returns HTML View (`return View()`).
* JsonResult returns JSON-formatted data (`return Json(data)`).
* ContentResult returns plain text (`return Content("plain text)`)..
* RedirectResult redirect to URL (`return Redirect("url")`).
* RedirectToActionResult redirects to another action (`return RedirectToAction("ActionName)`).
* StatusCodeResult returns a specific HTTP status code(`return StatusCode(404)`).

#### Action Params

* Way for action method to receive the input data.
* Can come from URL segments, query strings or form data.

Examples

```csharp
using Microsoft.AspNetCore.Mvc;       
using MyApp.Models; 

namespace MyApp.Controllers
{
	// This is our controller for handling requests to /Items
	public class ItemsController : Controller
	{
		// This is a simple action method returning data from the id parameter in URL. It takes an int called id (same as in the route pattern we defined earlier  pattern: "{controller=Home}/{action=Index}/{id?}")
		publict IActionResult PrintId(int id)
		{
			// This returns plain text printed on the screen
			return Content($"ID value from URL is: {id}");
		}
	}
}

// When url is visited with baseurl/items/printid/5 or
// baseurl/items/printid?id=5
// It will print ID value from URL is: 5
```

## Razor Syntax

Combines HTML and C# code.
It's an html document where you can add C# code by using `@{//c# code goes here}` syntax.

See [[razor|here]].

## EF Core

Entity Framework Core is a toolset for accessing databases using C4# object syntax.

Two main approaches here:
1. Code First
	* Define database schema using C# classes, then generate database from it.
2. Database First
	* Other way around.

To add it, we need to install few NuGet packages:
1. Solution Explorer > Dependencies > Manage NuGet packages
2. Search and install:
	1. Microsoft.EntityFrameworkCore
	2. Microsoft.EntityFrameworkCore.Tools
	3. Microsoft.EntityFrameworkCore.SqlServer
	

### Code First Approach

We create `Data` folder in the project, where we can store the models related to the data from the database.  In it, we create a class for the database context and configure it to inherit from the `DbContext` class from the EntityFrameworkCore which will provide all the database related goodies.

Steps:
1. Create database and copy connection string
	1. Visual Studio > View > Server Explorer > Right click "Data Connections" 
	2. Select "Create New SQL Server Database" > Enter server name, connection details and database name
	3. Once created, it will show under "Data Connections". Right click on it > Properties > Copy the connection string
2.  In the `application.json` file we need to declare the connection by adding `ConnectionStrings`:
   
	```json
	{
	  "Logging": {
		"LogLevel": {
		  "Default": "Information",
		  "Microsoft.AspNetCore": "Warning"
		}
	  },
	  "AllowedHosts": "*",
	  "ConnectionStrings": {
		"DefaultConnectionString": "your connection string pasted here"
	  }
	}
	
	```
	
	- Name of the connection string can vary.
	- Realistic example of connection string in a development environment using Visual Studio included SQL lite database engine:
	  
	  ```json
		// If using locally installed SQL Server:
		"ConnectionStrings": { "DefaultConnection": "Server=yourLaptopName\\SQLExpress;Database=ToDoAppDb;Trusted_Connection=True;MultipleActiveResultSets=true;TrustServerCertificate=True" }
		// "DefaultConnection" is the name we will use to reference this string. 
		// "Server=yourLaptopName\\SQLExpress" connects to the local SQL Server instance. 
		// "Database=ToDoAppDb" names our database. 
		// "Trusted_Connection=True" uses Windows authentication. 
		// "MultipleActiveResultSets=true" allows multiple queries on a single connection.
		// TrustServerCertificate=True allows authentication even if certificate is not trusted.
	  ```
	- Realistic example of connection string in a development environment on locally installed SQL server:

	  ```json
		// If using lite SQL db engine in the Visual Studio:
		"ConnectionStrings": { "DefaultConnection": "Server=yourLaptopName\\SQLExpress;Database=ToDoAppDb;Trusted_Connection=True;MultipleActiveResultSets=true;TrustServerCertificate=True" }
		// "DefaultConnection" is the name we will use to reference this string. 
		// "Server=(localdb)\\mssqllocaldb" connects to the local SQL Server instance. This is a special connection string for a lightweight, local instance of SQL Server that comes with Visual Studio. It's perfect for development.
		// "Database=ToDoAppDb" names our database. 
		// "Trusted_Connection=True" uses Windows authentication. 
		// "MultipleActiveResultSets=true" allows multiple queries on a single connection.
	  ```

3. Create a Model Class to represent the structure of a table
	- This actually represents the shape of a single item (what properties it consists of) but will be used as a table schema by the EF Core engine to create a table with pluralized name automatically (Item > table name Items)-
   
   ```csharp
	namespace MyApp.Models
	{
	    // Represents the structure of a "Item" record
	    // This will be used as a schema to create Items (plural) table
	    public class Item
	    {
	        public int Id { get; set; }            // Primary Key
	        public string Name { get; set; }       // Item's name
	        public bool IsActive { get; set; }     // Status flag
	    }
	}

	```
	
4. Create a Database Context Class (service for handling the database communication)
	1. Create `Data` folder in you project (by convention).
	2. In it, create a class which will be used to convey all the database handlers inherited from the `Microsoft.EntityFrameworkCore`'s `DbContext` method.
	   
	```csharp
	using LearningMVC.Models;
	using Microsoft.EntityFrameworkCore;
	
	namespace LearningMVC.Data
	{
	    // We create the context class here
	    // And configure it to inherit friom the DbContext class which will provide all the database related functionality
	    // Represents the database and its tables via DbSets
	    public class MyAppContext : DbContext
	    {
	        // Constructor to receive options (e.g. connection string)
	        public MyAppContext(DbContextOptions<MyAppContext> options) : base(options) { }
	
	        // Each DbSet represents a table
	        public DbSet<Item> Items { get; set; }
	    }
	}
	```
	
5. Add the service we created to the application, passing it to `AddDbContext()` method which gets a connection string passed to it as a option inside the `UseSqlServer()` method.
   
   ```csharp
	// This allows app to communiucate with the database (we're adding dbcontext service we created ourseleves in Data/)
	builder.Services.AddDbContext<MyAppContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnectionString")));
	```

6. Create initial Migration and update the database
	1. In the console (Tools > Nuget Package Manger Console) execute lines below.
	2. This will locally create `Migrations` folder where the database schema model is defined.
	3. This will also create tables in the database per that schema.
	4. Needs to be repeated whenever you add  new items in the model or otherwise change database schema.
	   
	   ```powershell
		Add-Migration "Initial Migration"
		Update-Database
		```

## Resources

1. Good basic crash course by FreeCodeCamp [here](https://www.youtube.com/watch?v=RWXKysImabs).