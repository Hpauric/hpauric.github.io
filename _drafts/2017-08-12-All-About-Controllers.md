---
published: false
---

## Controllers


> Rather than having a direct relationship between the URL and a file living on the web server’s hard
drive, a relationship exists between the URL and a method on a controller class

> AccountController: Responsible for account-related requests, such as login and account
registration

> a controller can pass data or objects to a view template using the `ViewBag` object. The `ViewBag` is a dynamic object that provides a convenient late-bound way to pass information to a view.

## Views

> The key transition character in Razor is the “at” sign (@). This single character is used to transition
from markup to code

### Details View

> By including a `@model` statement at the top of the view template file, you can specify the type of object that the view expects. 

### Index View

> `@model IEnumerable<MvcMovie.Models.Movie>`

> This @model directive allows you to access the list of movies that the controller passed to the view