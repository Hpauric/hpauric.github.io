---
layout: post
title: How to Update Your Database Structure in Entity Framework
published: true
image_url: /images/400-300px/Artur-Rydzewski-400-300.png
excerpt: >-
  There is a range of database initializers you can use in different stages of
  the development process. For a production database, Migrations allow you
  change your database structure without loss of data.
description: >-
  There is a range of database initializers you can use in different stages of
  the development process. For a production database, Migrations allow you
  change your database structure without loss of data.
---

Using Code First with Entity Framework, EF will create a database for you based on your domain models. But how do you control when and how the database will be created, and how the structure is updated?

Entity Framework 6.x includes a range of database initializers that specify how and when EF should generate a new database. These database initializers are intended to be used in different stages of the development process. By default, Entity Framework will create a database **only if one does not already exist** using the `CreateDatabaseIfNotExists` class. However, during development Entity Framework can be configured to drop and recreate the database every time there's a schema change.

**Note**: In this post I cover how to use database initializers with Entity Framework 6.x in a ASP.NET project. Entity Framework Core doesn't include database initializer classes.

## Drop and Recreate Your Database

During the development process, the domain models will be changing often. You can configure Entity Framework to delete (drop) and recreate your database every time there is a change to your schema.

To do this, you need to create an initializer class in the same folder as your `DbContext` class.  The class needs to inherit from `DropCreateDatabaseIfModelChanges`.
```csharp
 public class YourInitializer: System.Data.Entity
 .DropCreateDatabaseIfModelChanges<YourDbContext>
    {
    Seed Method
    ....
    }
```
To tell Entity Framework to use your initializer class, add an element to the `entityFramework` element in the application `Web.config` file (this should be in the root of your project folder):
```xml
<entityFramework>
  <contexts>
    <context type="Namespace.YourDbContext, ProjectName">
      <databaseInitializer type="Namespace.YourInitializer, ProjectName" />
    </context>
  </contexts>
  ...
```

## Creating a `Seed` method

The Database initializer classes include a `Seed` method that you can override. EF will automatically call this method after recreating the database to populate it with data. 
For example:

```csharp
public class MyInitializer : System.Data.Entity
.DropCreateDatabaseIfModelChanges<MyContext>
{
   protected override void Seed(MyContext context)
    {
      var students = new List<Student>
      {
      new Student{FirstMidName="Carson",EnrollmentDate=DateTime.Parse("2005-09-01")},
      new Student{FirstMidName="Meredith",EnrollmentDate=DateTime.Parse("2002-09-01")},
      new Student{FirstMidName="Yan", EnrollmentDate=DateTime.Parse("2002-09-01")}
      };
      students.ForEach(s => context.Students.Add(s));
      context.SaveChanges();
      //...
```

### Testing
There is also a `DropCreateDatabaseAlways` initializer class you can use to drop and recreate your database  **every time** your application runs. This is useful during the testing stage.


## Using Migrations


Once your database is ready for production, none of the above database initializers are going to be suitable for use. If you want to make changes to a database in production, you don't want to drop the entire database and lose all of the data. Code First Migrations allow you to update your database structure without having to drop and re-create the database.

To install the Migration package, click: 

 - `Tools` 
 -  `Nuget Package Manager`  
 - `Package Manager Console`

and type:
```powershell
Enable-Migrations
```

You will need only three CLI commands to use Migrations in your ASP.NET EF project.

| Command                         | Description                                                  |
| ---------------- | ----------------------- |
| `Enable-Migrations` | Install migration modules - this only needs to be run once initially |
| `Add-Migration [MigrationName]` | Generate a new migration code file based on changes in your project models |
| `Update-Database` | Implement any outstanding migrations                         |

### Migration Files

Migrations are code files. Every time you run `Add-Migration` a code file is generated, usually in a folder called `Migrations` inside your project. The name of a migration file is composed with a timestamp of its generation concatenated with the name used when running `Add-Migration`. For example:

```powershell
Add-Migration Initial
```
generates
```powershell
201710021217244_Initial.cs
```
You can check the contents of those files and see the generated migration file. You can also modify them like any other code file.

It's useful to think of migrations as git commits for your database. They document changes to your schema over time, and each migration builds on the last. Over time, your migrations folder will look something like this:

```powershell
Migrations
│   Configuration.cs
│   201801170953558_initial.cs
│   201802041312365_transactions-added.cs
│   201802041329270_transaction-additional-fields.cs
│   201802042308138_transaction-added-as-navigation-property.cs
│   201802111402054_purchased-added-to-transaction-type.cs
└───SeedData
```

In addition to the Migration code files, EF creates a table called `__MigrationsHistory` that contains information about all of the migrations that have been run in your database.

### Up and Down Methods

Every migration file has an `Up` method and a `Down` method. The `Up` method updates the database. The `Down` method reverts them. Here's a simple change as an example:


```csharp
namespace Project.Migrations
{
    public partial class TransactionModelUpdated : DbMigration
    {
        public override void Up() // Contains the code to implement the changes
        {
            AddColumn("dbo.Transaction", "Note", c => c.String());
        }   
        public override void Down()
        {
            DropColumn("dbo.Transaction", "Note");
        }
    }
}
```

The `Down` method is invoked with:
```powershell
Remove-Migration  [options]
```
However, you will probably never have to use this.
> I have never needed the ‘undo’ a migration command, and a quick survey of EF users at a recent talk I gave came back with the answer that none of them had ever used the ‘undo’ migration commands either.
> -- Jon P Smith, Entity Framework Core in Action

## The Configuration File

There is a configuration class for EF Migrations called `DbMigrationsConfiguration` class. 
```csharp
internal sealed partial class Configuration :
DbMigrationsConfiguration<ProjectName.DAL.DBContextName>
{
```
This class has its own `Seed` method that will run every time the `Update-Database` Powershell command is executed, even if there is no explicit pending migration. 
Before using the Migrations Seed method, you'll need to disable the database initializer by commenting out or deleting the `<databaseInitializer>` element you added in the `Web.config` file.

Since we are no longer dropping and recreating the database, the Migrations `Seed` method must be able handle the data already contained in the database. There is an extension method, `AddOrUpdate`, that is specifically designed for this purpose.

```csharp
protected override void Seed(ProjectName.DAL.DBContextName context)
{
  context.Students.AddOrUpdate(
  s => s.LastName,
  new Student { StudentID = 12345678,
  FirstMidName="Carson", LastName="Arturo" }, );
}
```

Instead of loading data into the database, `AddOrUpdate` will only add the entry if it doesn't currently exist in the database. If the entry doesn't match the new database schema, it will update accordingly.

It's important to note that you will need all of the fields for your record when you are using `AddOrUpdate`. For example, if the above record already exists in the database, but also included an `EmailAddress` field `carson.arturo@email.com`, this would be set to null in the update, since it wasn't provided to the `AddOrUpdate` method.
Julie Lerman (One of the creators of Entity Framework) writes [more about `AddOrUpdate` more here.](http://thedatafarm.com/data-access/take-care-with-ef-4-3-addorupdate-method/)



