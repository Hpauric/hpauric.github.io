---
published: false
---

I would like to have changes to the database logged. And also have instances of the database saved that I can revert to.

[Azure Database Automated Backups](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automated-backups)


> ### How long do you keep my backups?
Each SQL Database backup has a retention period that is based on the service-tier of the database. The retention period for a database in the:
Basic service tier is 7 days.
Standard service tier is 35 days.

You can set up SQL database backups for a period of up to 10 years. However at the time of this writing in Europe this is still just in preview mode.

### Azure Storage Concepts

When using SQL Server Data Files in Windows Azure feature, you need to create a **storage account** and a **container** in Windows Azure. 
Then, you need to create a **SQL Server credential**, which includes information on the policy of the container as well as a shared access signature that is necessary to access the container.
In Microsoft Azure, an Azure **storage account** represents the highest level of the namespace for accessing Blobs. A storage account can contain an unlimited number of **containers**. A **container** provides a grouping of a set of Blobs. All Blobs must be in a container. 
A container can store an unlimited number of **Blobs**. There are two types of blobs that can be stored in Azure Storage: block and page blobs. You can access Blobs using the following URL format: http://storageaccount.blob.core.windows.net/<container>/<blob>.


## Viewing the Transaction Logs

**Azure SQL DB does not expose the log.**

 the transaction log file is differently handled in SQL Azure, and cannot be physically read by a tool.
 
 The way to view the transaction logs in Azure is to **enable Auditing** [by following these instructions](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing#subheading-2).
 
[Here's a list of SQL Server features and whether they are supported in Azure SQL Database.](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-features)


## Application Insights

Failed Requests
Dependency failed calls

