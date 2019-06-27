---
layout: post
title: How to Remove Duplicate Records from a Table with SQL
published: true
image-attributions: Photo by Giallo from Pexels
image_url: /images/400-300px/duplicate-400-300.png
excerpt: >-
 In this guide, I walk through how to check you table for duplicates, and how to remove them.
---



* ToC
{:toc}


## Intro

There are certain situations where duplicate records might get into your database. For example, if you have multiple routes to onboarding new users, you can end up with duplicate records in your User table.

I came across this problem working on my project [Mana Wisdom](https://manawisdom.com/), when I was importing orders into my database. I needed to check if any duplicates had been imported by mistake.

In this guide, I walk through how to check you table for duplicates, and how to remove them.



## Confirm that you do have duplicate records

Before removing duplicates, you need to check if you have any duplicates in the first place. The columns you want to check for depends on the type of duplication you suspect. The easiest place to start is to check for primary key duplicates.

### Check for duplicate primary keys

To do this, query the table and aggregate them by their primary key, in this case `id`. Then use the `HAVING` clause to only return `id`s with a count greater than 1:

```sql
SELECT id, COUNT(*) as Count
FROM
    Orders
GROUP BY
    id
HAVING 
   COUNT(*) > 1 
```

Here we see that there are a few records with duplicate records, and one (11457) with triplicate records:

{: .table .table-bordered .table-dark}
| ID    | Count |
| ----- | ----- |
| 323   | 2     |
| 2244  | 2     |
| 3467  | 2     |
| 11457 | 3     |

### Check for duplicate names

If you want to check for duplicate users, you'll want to check for their name and some other identifier like their address, or in this case email address:


```sql
SELECT firstname, lastname, email, COUNT(*) as Count
FROM
    Persons
GROUP BY
    firstname, lastname
HAVING 
   COUNT(*) > 1 
```

{: .table .table-bordered .table-dark}
| FirstName | LastName            | Email                     | Count |
| --------- | ------------------- | ------------------------- | ----- |
| Mad Dog   | Jones               | maddog.jones@gmail.com    | 2     |
| Marcus    | Angstrom            | marcus.angstrom@gmail.com | 2     |
| Surly     | Chekov              | surly.chekov@gmail.com    | 2     |
| Cthulhu   | Destroyer of Worlds | cthulthu@gmail.com        | 3     |

## Viewing the duplicates full records

Before you delete the duplicate records, you might want to view other columns on the records. There may be information that indicates which is the canonical record. Alternatively, you might want to merge information on two records. Or you may want to find out how the duplicate records were created, to prevent it from happening again.

To do this, use the query above, but now encase it in a common table expression:
```sql
;WITH Duplicates AS 
    (SELECT asset_id,
         COUNT(*) AS count
    FROM publications
    GROUP BY  asset_id
    HAVING COUNT(*) > 1)
```
Then you can join it to the table you are querying, to get all of the columns:
```sql
SELECT p.*, d.count
FROM Duplicates d
INNER JOIN Publications p
    ON p.asset_id = d.asset_id 
```

Now you can decide which record to keep, and whether to merge any fields:

{: .table .table-bordered .table-dark}

| FirstName | LastName | Email                     | Date Created | Source              | Count |
| --------- | -------- | ------------------------- | ------------ | ------------------- | ----- |
| Mad Dog   | Jones    | Mad.Dog.Jones@gmail.com   | 01/02/2017   | Online Subscription | 2     |
| Mad Dog   | Jones    | Mad.Dog.Jones@gmail.com   | 04/02/2018   | Company Membership  | 2     |
| Marcus    | Angstrom | Marcus.Angstrom@gmail.com | 11/11/2019   | Company Membership  | 2     |
| Marcus    | Angstrom | Marcus.Angstrom@gmail.com | 01/02/2017   | Online Subscription | 2     |



## Removing Duplicates: The Straightforward Way

Ok, so you've decided you want to delete the duplicate records.

It's easier to create a new table with just one row for each distinct value, rather than trying to delete the extra records so that only one valid record remains.

To do this, create a common table expression with a list of distinct records that you want to keep. Then `INNER JOIN` the resulting rowset to the table you are working on and save it as a temporary table.

```sql
;WITH Duplicates AS 
    (SELECT distinct(asset_id)
    FROM Orders)
SELECT p.*
INTO #TempTable
FROM Duplicates d
INNER JOIN Orders p
    ON p.asset_id = d.asset_id 
```

You can then `DELETE` your table and `INSERT` the contents of the temporary table.

```sql
DELETE Orders

INSERT Orders
SELECT *
FROM #TempTable

DROP TABLE #TempTable
```

