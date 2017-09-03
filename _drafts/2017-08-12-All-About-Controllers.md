---
published: false
---





## c#

### attributes

Attributes are effectively metadata that describe the property, method or class that they precede. They are encased in square brackets.

```csharp
[Display(Name = "Release Date")] // attribute
public DateTime ReleaseDate { get; set; }
```

### G-eneric Collections

`List<T>`	T stands for type. This is the new Array. It’s a typed list of items. By
far, the most common collection class used. It’s the default.
If you need a list of people, it’s a `List<Person>`

## ASP.NET Classes Cheatsheet

Classes
- IEnumerable System.Collections.Generic
- DbContext System.Data.Entity
- Controller System.Web.Mvc
- HTMLHelper  System.Web.Mvc

Methods


```
## Controllers


> Rather than having a direct relationship between the URL and a file living on the web server’s hard
drive, a relationship exists between the URL and a method on a controller class

> AccountController: Responsible for account-related requests, such as login and account
registration

> a controller can pass data or objects to a view template using the `ViewBag` object. The `ViewBag` is a dynamic object that provides a convenient late-bound way to pass information to a view.

## Views

> The key transition character in Razor is the “at” sign (@). This single character is used to transition
from markup to code

###  Layout

By convention, the default layout for an ASP.NET app is named `_Layout.cshtml`.

The default layout is defined in `_ViewStart.cshtml` file in `Views`.

### Details View

> By including a `@model` statement at the top of the view template file, you can specify the type of object that the view expects. 

### Index View

> `@model IEnumerable<MvcMovie.Models.Movie>`

> This @model directive allows you to access the list of movies that the controller passed to the view

### `ActionLink` Method


`Html.ActionLink`

The `Html` object is a helper that's exposed using a property on the `System.Web.Mvc.WebViewPage base` class. The `ActionLink` method of the helper makes it easy to dynamically generate HTML hyperlinks that link to action methods on controllers. 

```csharp
@Html.ActionLink("Edit", "Edit", new { id=item.ID }) 
```
arguments
- string linkText
- string actionName (this is the method name unless otherwise specified)
- string controllerName OR object routeValues


The default route (established in `App_Start\RouteConfig.cs)` takes the URL pattern `{controller}/{action}/{id}`

So for example,the generated route is:

http://localhost:1234/Movies/Edit/4



### Html.DisplayFor

### Edit View
