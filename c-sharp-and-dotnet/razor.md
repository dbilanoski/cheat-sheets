---
tags: [csharp, dotnet, mvc, chtml]
---
# Razor Syntax

Razor is the templating engine used in ASP.NET Core MVC to combine HTML and C# code. It's also used in:
- ASP.NET Core Razor Pages
    - A page-based alternative to MVC, also using Razor syntax.
    - Still uses `.cshtml` files, but organized differently.
- Blazor (ASP.NET Core)
    - Uses Razor components (`.razor` files).
    - Can run in the browser (WebAssembly) or server.
    - Same syntax, different runtime concept.

This note covers the most common and useful syntax you'll use in `.cshtml` view files.

## Syntax Elements
### Model Declaration

Defines the model type that the view expects to receive from the controller.

```csharp
@model MyApp.Models.User
```

### Accessing Model Properties

Use `@Model.PropertyName` to output values from the model.

```html
<p>@Model.Name</p>
<p>ID: @Model.Id</p>
```

### C# Code Blocks

Run inline C# code or declare variables in the view.

```csharp
@{
    var greeting = "Hello";
    int year = DateTime.Now.Year;
}
<p>@greeting - @year</p>
```

### Control Structures

Use C# control flow like `if`, `for`, and `foreach`.

```csharp
@if (Model.Id == 1)
{
    <p>Admin User</p>
}
else
{
    <p>Regular User</p>
}

@foreach (var user in Model.Users)
{
    <li>@user.Name</li>
}

@for (int i = 0; i < 5; i++)
{
    <p>Index: @i</p>
}
```

### HTML Encoding and Raw Output

By default, Razor encodes HTML to prevent injection. Use `Html.Raw` to write raw HTML when needed.

```csharp
@Model.Name            // Encoded (safe)
@Html.Raw("<b>Bold</b>") // Not encoded (be careful)
```

### Razor Comments

These comments won't appear in the rendered HTML.

```csharp
@* This is a Razor comment *@
```

### Render Partial Views

Render reusable `.cshtml` files (partial views) and pass data to them.

```csharp
@Html.Partial("_UserCard", user)
```

### Generating Links

Use helpers to generate URLs and anchor tags.

```csharp
<a href="@Url.Action("Details", "Home", new { id = 5 })">View Details</a>

<!-- Preferred: Tag Helpers -->
<a asp-controller="Home" asp-action="Details" asp-route-id="5">Details</a>
```

### Forms and Input Fields

Use tag helpers to bind form inputs to model properties.

```html
<form asp-controller="Home" asp-action="Submit" method="post">
    <input type="text" asp-for="Name" />
    <span asp-validation-for="Name"></span>
    <button type="submit">Submit</button>
</form>
```

### Layouts

Define and use layout files for consistent page structure.

```csharp
@{
    Layout = "_Layout";
}
```

In `_Layout.cshtml`:

```html
<body>
    @RenderBody()
</body>
```

### Sections

Use sections to inject content into specific parts of the layout.

In layout file:

```csharp
@RenderSection("Scripts", required: false)
```

In a view:

```csharp
@section Scripts {
    <script src="site.js"></script>
}
```

### HTML Helpers

Helpers to display, edit, and validate model fields.

```csharp
@Html.DisplayFor(model => model.Name)
@Html.EditorFor(model => model.Name)
@Html.LabelFor(model => model.Name)
@Html.ValidationMessageFor(model => model.Name)
```

### Conditional Attributes

Set class names or other attributes conditionally.

```html
<div class="@(Model.IsActive ? "active" : "inactive")">
    @Model.Name
</div>
```


## Example Razor View File

An example `Details.cshtml` file showing core Razor syntax in aciton.

```html
@model MyApp.Models.User
<!-- Declares that this view expects a User model from the controller -->

@{
    ViewData["Title"] = "User Details";
    var currentYear = DateTime.Now.Year;
}
<!-- Sets the page title and defines a local variable -->

<h1>@ViewData["Title"]</h1>
<!-- Outputs the page title from ViewData -->

<p>Name: @Model.Name</p>
<!-- Displays the user's name from the model -->

<p>User ID: @Model.Id</p>
<!-- Displays the user's ID -->

<p>Joined: @currentYear</p>
<!-- Outputs the current year using a local variable -->

@if (Model.IsActive)
{
    <p>Status: <strong>Active</strong></p>
}
else
{
    <p>Status: <strong>Inactive</strong></p>
}
<!-- Uses an if-statement to conditionally display user status -->

<ul>
    @foreach (var hobby in Model.Hobbies)
    {
        <li>@hobby</li>
    }
</ul>
<!-- Loops through a list of hobbies and displays each one -->

@* This is a Razor comment and will not appear in the HTML *@

<!-- Render a partial view to show additional user info -->
@Html.Partial("_UserContactInfo", Model.ContactInfo)

<!-- Button linking back to user list -->
<a asp-controller="Home" asp-action="Index">Back to List</a>
```