---
published: false
---
Entity Framework allows you to develop a data model with a Code First approach.

Code First Migrations allow you to update your data model and update the database schema without having to drop and re-create the database.

This means that you can define your models and Entity Framework will create the database for you.

## The Connection String

A connection string provides the information that a provider needs to communicate with a particular database. The Connection String includes parameters such as the name of the driver, Server name and Database name , as well as security information such as user name and password.


First of all, you need to add a connection string to your `Web.config` file. Enter this text in the Web.config file in your project.

```html
<connectionStrings>
    <add name="[YOURMODELNAMEHERE]Context"    
         connectionString="Data Source=(LocalDB)\MSSQLLocalDB;
  AttachDbFilename=|DataDirectory|\[YOURDATABASENAMEHERE].mdf;
  Integrated Security=True" 
         providerName="System.Data.SqlClient"/>
  </connectionStrings>
```
Note that In Visual Studio 2012 the data source is `(LocalDB)\v11.0`. For Visual Studio 2015 and Visual Studio 2017 it is `(LocalDB)\MSSQLLocalDB`.
LocalDB is a lightweight version of the SQL Server Express Database Engine that is targeted for program development. LocalDB starts on demand and runs in user mode, so there is no complex configuration. 


You don't actually have to have a connection string in the Web.config file. If you don't supply a connection string, Entity Framework will use a default one based on your context class. However in that case the data won't save to a database.

### Deploying to Azure

New connection strings are generated when you deploy to Azure.

```html
<connectionStrings>
    <add name="ProjectContext"
         connectionString="$(ReplacableToken_ProjectContext-Web.config Connection String_0)" 		providerName="System.Data.SqlClient" />
    <add name="ProjectContext_DatabasePublish" connectionString="$(ReplacableToken_ProjectContext_DatabasePublish-Web.config Connection String_0)" providerName="System.Data.SqlClient"/>
  </connectionStrings>
```

You can find them in `[ProjectName]\obj\Release\Package\PackageTmp\Web.config`.

## Setting the Seed method to update from a csv file.

Rather than painstakingly typing values into the Seed method, you can set it to import from a CSV file.
[There are great instructions on how to do this here.](https://www.davepaquette.com/archive/2014/03/18/seeding-entity-framework-database-from-csv.aspx)
One mistake I made at first was that I referenced the wrong assembly. Stack Overflow recommended "to make sure you're in the right assembly and with right name: dump and evaluate all the resources available in your target assembly." Which I did:

```csharp
string[] names = assembly.GetManifestResourceNames();
string fullString = "";
names.ToList().ForEach(i => fullString += (i.ToString()) + '\n');
```
### Debugging the Update-Database command

Running the Update-Database command meant that debugging statments like `Debug.WriteLine()` didn't work. Instead I threw an error:

```csharp
throw new Exception(fullString);
```
Then I could see I couldn't access the resource since:
1. It was saved in the wrong namespace.
2. It's build action was set to `Resource` instead of `Embedded Resource`.

After I fixed these mistakes I could easily Seed from a CSV.


## Migrations

First of all, you don't _have_ to use Migrations. However, if you don't, all database content will be lost when you update your models.

[Great introduction to Code First Migrations](https://stackoverflow.com/questions/40606167/error-when-update-database-using-code-first-there-is-already-an-object-named)

### NuGet Commands

There are three main NuGet commands used in migrations.
- `Enable-Migrations` - Used initially to install migration modules
- `Add-Migration MyNewMigration` - Used to add a migration
- `Update-Database`

Migrations are code files. Every time you run `Add-Migration` a code file is generated/updated, usually in a folder called `Migrations` inside your project. The name of a migration file is composed with a timestamp of its generation concatenated with the name used when running `Add-Migration`. For example
```bash
Add-Migration Initial
```
generates
```bash
201710021217244_Initial.cs
```



You can check the contents of those files and see the effects of running `Add-Migration`. You can also modify them once generated, and add your own code. From my experience it is often necessary to do this.

Migrations are intended to be incremental. You start with an `Initial` migration, and every time you change your model code you generate a new migration file. The database contains a table named `__MigrationsHistory` that keeps trace of which migrations have been run in your database.

### Up and Down Methods

Every single migration has a method `Up` and a method `Down`. 

### Migration History

When you run `Update-Database` there are always two implicit parameters: `SourceMigration` and `TargetMigration`. EF incrementally applies the `Up` methods of all the migrations between `SourceMigration` and `TargetMigration` (or the `Down` methods if you are downgrading your database). 

The default scenario when you don't specify the `SourceMigration` and `TargetMigration` parameters is that `SourceMigration` is the last migration applied to the database and `TargetMigration` is the last of the pending ones. EF determines those parameters by querying the `__MigrationsHistory` table of the default database of your project, so if that database is not in a consistent state, your migrations can be generated incorrectly.

## The Easiest Way to fix a problem

There is already an object named 'ModelName' in the database.

1. Delete the `Migrations` folder in your solution.
2. Delete the tables in the database.
3. Delete the `_MigrationHistory` table in the database.
4. Close the database connection.
5. In the NuGet console, run `Enable-Migrations`.
6. Run `Add-Migration Initial`.
7. Run `Update-Database`.




### When Things Go Wrong

Entity Framework is not perfect.

> Essentially the ORM can handle about 80-90% of the mapping problems, but that last chunk always needs careful work by somebody who really understands how a relational database works.

> Mapping to a relational database involves lots of repetitive, boiler-plate code. A framework that allows me to avoid 80% of that is worthwhile even if it is only 80%. The problem is in me for pretending it's 100% when it isn't.

You can't just run `Add-Migration MyNewMigration` and assume everything will work out fine. You need to check to make sure that the generated code works correctly.




