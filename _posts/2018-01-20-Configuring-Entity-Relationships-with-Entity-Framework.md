---
layout: post
title: Configuring Entity Relationships with Entity Framework
published: true
---

![pexels-photo-733740.jpeg]({{site.baseurl}}/images/electricity-pylons.jpeg)

> I used to laboriously define my relationships because I hadn’t fully understood the power of the *by convention* approach when it comes to relationships. 
> -- Jon P Smith, Entity Framework Core in Action

I've been reading [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action) by Jon P Smith. It's a fantastic book that's taught me a lot. One thing I've learned is to let Entity Framework manage the entity relationships in my projects, configuring the relationships myself only as a last resort.

Before jumping into an example, let's quickly cover the three ways to configure relationships in Entity Framework:
 1. There is the fluent API.
2. There are data annotations.
3. Finally, there is configuring *by convention*.

### The Fluent API
If I want a property in my entity to be required, I can configure it with the Fluent API in the `OnModelCreating` method, which is part of the `DbContext`:
```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder){
  modelBuilder.Entity<Equipment>()
  .Property(e => e.EquipmentType)
  .IsRequired();
}
```
### Data Annotations
I can also set a property to be required with data annotations. Using the same example, I can set `EquipmentType` to be required by including an annotation when declaring it in the model class:
```csharp
public class Equipment {
  [Required]
  public string EquipmentType { get; set; }
  // Other properties
}
```
### By Convention
So what is configuration by convention? 
> When you follow some simple rules on property types and names, Entity Framework will auto-configure many of the software and database features.

If I include a property called `EquipmentID` in my `Equipment` class, Entity Framework will configure that property to be the `Equipment` entity's *primary key*. This is because one of Entity Framework's default behaviours is to look for propertys ending in 'ID' and assign *keys* (*primary* or *foreign*) if they match class names in the *Model* folder.

## Configuring Relationships in Entity Framework

Configuring scalar (non-relational) properties is quite straightforward, but when it comes to entity relationships, it can be more tricky to decide which approach is best for configuring them.

### How to Cascade Set Null

In an application I am working on, I have a `Student` entity that has a ZeroOrOne-to-Many relationship with `Equipment` and `Transaction` entities.
By default, when I delete a `Student`, I will get a referential integrity error:
```bash
System.Data.SqlClient.SqlException: 
The DELETE statement conflicted with the REFERENCE constraint 
"FK_dbo.Transaction_dbo.Student_StudentID"
```
So how do I fix this? The [official Microsoft documentation](https://docs.microsoft.com/en-us/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application#update-the-deleteconfirmed-method) recommends manually deleting each foreign key for dependent entities:

```csharp

// load student
var equipment = db.Equipments
                    .Where(e => e.StudentID == student.StudentID)
                    .SingleOrDefault();
                if (equipment != null)
                {
                    equipment.StudentID = null;
                    db.SaveChanges();
                }
   ```
However, that's a lot of lines of code to do something that should be possible to configure as a default! It's especially a lot of code if you have multiple dependent entities, as I do in this case (`Equipment` and `Transaction`).
If we look at [the documentation for Entity Framework 6](https://msdn.microsoft.com/en-us/library/jj591620%28v=vs.113%29.aspx?f=255&MSPPError=-2147217396#Enabling%20Cascade%20Delete) it says that:

> If a foreign key on the dependent entity **is nullable**, Code First does not set cascade delete on the relationship, and when the principal is deleted **the foreign key will be set to null**.

### Configuring By Convention
So if I set both the Equipment and Transaction `StudentID` foreign key set to nullable, by using nullable `int?` like so:
```csharp
public class Equipment {
  public int? StudentID{ get; set; } 
  // Other properties
}
public class Transaction {
  public int? StudentID{ get; set; } 
  // Other properties
}
```
Entity Framework should now set the foreign keys to null for these entities when I delete a student. However as the code stands, I will still get the same referential integrity error.

### Loading Entities
So what's going on? The crucial detail here is that **the dependent entities must be loaded** before the principal entity is deleted. Here I explicitly load both the `Equipment` and `Transaction` entities before removing the `student` entity:

```javascript
// load student entity
db.Entry(student).Collection(s => s.Equipment).Load();
db.Entry(student).Collection(s => s.Transaction).Load();
db.Students.Remove(student);
db.SaveChanges();
```
When the `Equipment` and `Transaction` entities are loaded into the working memory, EF knows to set the relevant dependent entities foreign keys to null.
This is a good example of letting the conventions work for you!

### Configuring Relationships with The Fluent API

When I was working through this problem, I thought it was a configuration issue, and manually configured the relationships with the fluent API:

```javascript
protected override void OnModelCreating(DbModelBuilder modelBuilder)
   modelBuilder.Entity<Equipment>()
               .HasOptional<Student>(e => e.Student)
               .WithMany(s => s.Equipment)
               .HasForeignKey(e => e.StudentID)
               .WillCascadeOnDelete(false);
}
```
However I actually didn't need to set the Fluent API at all!  The default Entity Framework is to set null the foreign key on a dependent entity when the principal entity is deleted.

 So when would I need to use the Fluent API? If I wanted to  **override** the default delete behavior setting, for example to set the dependent entities to cascade on delete, then the Fluent API would be the place to configure that.
In EF Core in Action, Smith outlines some other examples of when you might need to use the Fluent API:
  - When you want to create a one-to-one relationship without navigational links going both ways. 
  - When you have two navigational properties going to the same class. 
 -  If you want to define a specific database constraint.
