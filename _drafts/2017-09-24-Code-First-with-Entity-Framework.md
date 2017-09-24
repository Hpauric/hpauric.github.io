---
published: false
---
Entity Framework allows you to develop a data model with a Code First approach.

Code First Migrations allow you to update your data model and update the database schema without having to drop and re-create the database.

This means that you can define your models and Entity Framework will create the database for you.

## The Connection String

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
You don't actually have to have a connection string in the Web.config file. If you don't supply a connection string, Entity Framework will use a default one based on your context class. However in that case the data won't save to a database.

## Deploying to Azure

New connection strings are generated when you deploy to Azure.

```html
<connectionStrings>
    <add name="ProjectContext"
         connectionString="$(ReplacableToken_ProjectContext-Web.config Connection String_0)" 		providerName="System.Data.SqlClient" />
    <add name="ProjectContext_DatabasePublish" connectionString="$(ReplacableToken_ProjectContext_DatabasePublish-Web.config Connection String_0)" providerName="System.Data.SqlClient"/>
  </connectionStrings>
```

You can find them in `[ProjectName]\obj\Release\Package\PackageTmp\Web.config`.


## NuGet Commands

`Enable-Migrations`
`Add-Migration`
`Update-Database`