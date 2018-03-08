---
layout: post
title: Database Updates and Migrations with Entity Framework
published: true
image_url: /images/400-300px/Artur-Rydzewski-400-300.png
excerpt: You can update your database with Code First Migrations.
description: You can update your database with Code First Migrations.
---

# Database Updates and Migrations with Entity Framework

Entity Framework (EF) allows you to abstract away interactions with you database. But how do you update your database when your entities change? 
EF has two ways you can manage changes to your schema:

 1. Drop and recreate your database 
 2. Use migrations

## Drop and Recreate Your Database

You can configure Entity Framework to drop and recreate your database every time there is a change to your schema, or just literally every time your application runs. 

You can also write your own `Seed` method that EF will automatically call after recreating the database to populate it with data. 

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
```html
<entityFramework>
  <contexts>
    <context type="Namespace.YourDbContext, ProjectName">
      <databaseInitializer type="Namespace.YourInitializer, ProjectName" />
    </context>
  </contexts>
  ...
```

## Creating a `Seed` method

You can use a Seed method to populate your database with mock data. For example:

```csharp
namespace ContosoUniversity.DAL
{
    public class SchoolInitializer : System.Data.Entity. DropCreateDatabaseIfModelChanges<SchoolContext>
    {
        protected override void Seed(SchoolContext context)
        {
            var students = new List<Student>
            {
            new Student{FirstMidName="Carson",EnrollmentDate=DateTime.Parse("2005-09-01")},
            new Student{FirstMidName="Meredith",EnrollmentDate=DateTime.Parse("2002-09-01")},
            new Student{FirstMidName="Arturo"EnrollmentDate=DateTime.Parse("2003-09-01")},
            new Student{FirstMidName="Gytis",EnrollmentDate=DateTime.Parse("2002-09-01")},
            new Student{FirstMidName="Yan", EnrollmentDate=DateTime.Parse("2002-09-01")}
            };
            students.ForEach(s => context.Students.Add(s));
            context.SaveChanges();
```
## Using Migrations

Code First Migrations allow you to update your data model and update the database schema without having to drop and re-create the database.

You don't _have_ to use Migrations. However, if you don't, all database content will be lost when you update your models. You probably don't want that to happen to your production database ;)

So you can't use the `DbContext` Initializer class. Luckily EF comes with a migrations package that allows you to make changes to a production database without nuking it.
What kind of changes? Adding a new entity, adding new columns, changing a foreign key. There are a lot of options.

To get started, install the migration modules at the powershell console:

```powershell
Enable-Migrations
```

You just need three CLI commands to use Migrations in your .NET EF project.

| Command                         | Description                                                  |
| ------------------------------- | ------------------------------------------------------------ |
| `Enable-Migrations`             | Install migration modules - this only needs to be run once initially |
| `Add-Migration [MigrationName]` | Generate a new migration code file based on changes in your project models |
| `Update-Database`               | Implement any outstanding migrations                         |

### Migration Files

Migrations are code files. Every time you run `Add-Migration` a code file is generated, usually in a folder called `Migrations` inside your project. The name of a migration file is composed with a timestamp of its generation concatenated with the name used when running `Add-Migration`. For example:

```powershell
Add-Migration Initial
```
generates
```powershell
201710021217244_Initial.cs
```
You can check the contents of those files and see the generated migration file.  You can also modify them.

It's useful to think of migrations as git commits for your database. Some people prefer big infrequent commits and some people like to do them small and regular. Over time, your project will probably amass a string of migrations, each building on the last.

>  Migrations are intended to be incremental. You start with an `Initial` migration, and every time you change your model code you generate a new migration file. For example, `201712121451507_type-correction-date-purchased.cs`. The database contains a table named `__MigrationsHistory` that keeps trace of which migrations have been run in your database.

Over time, your migrations folder will look something like this:

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
>I have never needed the ‘undo’ a migration command, and a quick survey of EF users at a recent talk I gave came back with the answer that none of them had ever used the ‘undo’ migration commands either.
> -- Entity Framework Core in Action

## The Configuration File

There is a configuration class for EF Migrations called `DbMigrationsConfiguration` class. This class has its own `Seed` method that will run every time the `Update-Database` Powershell command is executed, even if there is no explicit pending migration. 

```csharp
 internal sealed partial class Configuration : DbMigrationsConfiguration<YourDbContext>
    {
```

> Note: This is in contrast to the Database initializer `Seed` method, which will only run when the database is created. [Database initializer and Migrations Seed methods](https://blog.oneunicorn.com/2013/05/28/database-initializer-and-migrations-seed-methods) is a great blog post that covers this in more detail.
>
Before using the Migrations Seed method, you'll need to disable the database initializer by commenting out or deleting the `<databaseInitializer>` element you added in the `Web.config` file.

Since we are no longer dropping and recreating the database, the Migrations `Seed` method must be able handle the data already contained in the database. There is an extension method, `AddOrUpdate`, that is specifically designed for this purpose.

```csharp
internal sealed partial class Configuration : DbMigrationsConfiguration<ProjectName.DAL.DBContextName>
    {
        // Constructor
        protected override void Seed(ProjectName.DAL.DBContextName context)
        {
        context.Students.AddOrUpdate(
        s => s.LastName,
        new Student { StudentID = 12345678,
        FirstMidName="Carson", LastName="Arturo" }, );
        }
```

Instead of loading data into the database, `AddOrUpdate` will only add the entry if it doesn't currently exist in the database. If the entry doesn't match the new database schema, it will update accordingly.

The first parameter passed to the AddOrUpdate method specifies the property to use to check if a row already exists. For the test student data that you are providing, the LastName property can be used for this purpose since each last name in the list is unique:
```csharp
context.Students.AddOrUpdate(p => p.LastName, s)
```
This code assumes that last names are unique. If you manually add a student with a duplicate last name, you'll get the following exception the next time you perform a migration.
