---
published: false
---
Entity Framework allows you to develop a data model with a Code First approach.

This means that you can define your models and Entity Framework will create the database for you.

The connection string

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
