---
published: false
---


Why do we need Entity Framework?
You can use Entity Framework for code-first implementations. This allows you to define your models in code, and let Entity Framework generate the database for you.


## Using Entity Framework

> The main class that coordinates Entity Framework functionality for a given data model is the database context class. You create this class by deriving from the `System.Data.Entity.DbContext` class. In your code you specify which entities are included in the data model.

### `DbContext`

The `DbContext` class you create will usually be quite simple.

```csharp
    public class ProjectContext : DbContext
    {

        public ProjectContext() : base("ProjectContext")
        {
        }

        public DbSet<Student> Students { get; set; }
        public DbSet<Equipment> Equipments { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
        }
    }
```
### DbContext Methods




## Creating the Schema

If you look at the example application Microsoft use to demonstrate the features, you'll see that in the first excercise they create three entities:
- Student
- Course
- Enrollment

If you were creating your first application you might think you would just need two entities: `Student` and `Course`. Why do we need Enrollment? Why can't our Students have the course they are enrolled in as properties, and our Course have Students as properties? What does the `Enrollment` entity bring to the table?

> The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables. 

Is this true for the Enrollment entity? No. It has it's own EnrollmentID key.




### Connection String


Enter this text in the `Web.config` file in your project.

```html
<connectionStrings>
    <add name="[YOURMODELNAMEHERE]Context"    
         connectionString="Data Source=(LocalDB)\MSSQLLocalDB;
  AttachDbFilename=|DataDirectory|\[YOURDATABASENAMEHERE].mdf;
  Integrated Security=True" 
         providerName="System.Data.SqlClient"/>
  </connectionStrings>
```

You don't actually have to have a connection string in the `Web.config` file. If you don't supply a connection string, Entity Framework will use a default one based on your context class. However in that case the data won't save to a database.

### Navigation Properties

> Navigation properties provide a way to navigate an association between two entity types. Every object can have a navigation property for every relationship in which it participates. Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many). You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.

https://msdn.microsoft.com/en-us/library/jj713564(v=vs.113).aspx

```csharp
public virtual OfficeAssignment OfficeAssignment { get; set; }
```

If a navigation property can hold multiple entities, its type must implement the ICollection<T> Interface.
  
```csharp  
public virtual ICollection<Equipment> Equipment { get; set; }  
```

The `Equipment` property is a navigation property. Navigation properties hold other entities that are related to this entity. In this case, the `Equipment` property of a `Student` entity will hold all of the `Equipment` entities that are related to that Student entity. In other words, if a given `Student` row in the database has two related `Equipment` rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Equipment` navigation property will contain those two `Equipment` entities.

Navigation properties are typically defined as virtual so that they can take advantage of an Entity Framework feature called lazy loading.
