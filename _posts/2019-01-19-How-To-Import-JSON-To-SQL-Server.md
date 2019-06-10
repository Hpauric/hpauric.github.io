---
layout: post
title: How to Import JSON to SQL Server
published: true
image_url: /images/400-300px/assembly-line-400-300.png
excerpt: >-
 Using OPENROWSET and OPENJSON makes it easy for you to import JSON data into your database.
---

The JSON format is great for sharing data because it's portable, parseable and simple. But what do you do when you need to do some serious manipulation or analysis of the data? Or you just want to keep it around? Data worth keeping belongs in a database.

Luckily, SQL Server has some functions that make it easy for you to import JSON data into your database.

* ToC
{:toc}

## 1. Import the JSON as String Data

You can bulk import any kind of text file, including JSON files, into SQL with the [`OPENROWSET`](https://docs.microsoft.com/en-us/sql/t-sql/functions/openrowset-transact-sql?view=sql-server-2017) function.

To import the json as a text file, use the `OPENROWSET` function with the `BULK` option enabled.

`OPENROWSET` returns a single string field with `BulkColumn` as its column name.

Let's assign it to a variable so we can work with it:

```sql
DECLARE @JSON VARCHAR(MAX)

SELECT @JSON = BulkColumn
FROM OPENROWSET 
(BULK 'C:\file-location\my-data.json', SINGLE_CLOB) 
AS j
```
You need to pass in a second argument to tell `OPENROWSET` what kind of data type to import. Since this is a text string, use `SINGLE_CLOB`.[^fn1]

`OPENROWSET` will then read the file as `VARCHAR(MAX)`. This is the same data type as the `@JSON` variable we declared.

Use `ISJSON` to make sure the JSON is valid. `ISJSON` will return 1 if the string is a properly formatted JSON object. 

```sql
SELECT ISJSON(@JSON) 
```
You can select the variable to see the contents:
```sql
If (ISJSON(@JSON)=1)
SELECT @JSON AS 'JSON Text'
```

In this example, the JSON is from an API call for a set of land parcels from a virtual reality platform[^fn2] (to make things interesting &#x1f995;):


```javascript
{
  "ok": true,
  "data": {
    "assets": {
      "parcels": [{ 
        "id": "51,100",
        "x": 51,
        "y": 100,
        "auction_price": 4444,
        "district_id": "77909d00",
        "owner": "Dr Cornwallis",
        "data": { "version": 0 }
        }, 
        //...+ 2499 more objects in the parcels array
      ],
      total: 2500
    },
  },
}
```


When selected, the entire JSON will be listed in a single-column row:

{: .table .table-bordered .table-dark}
|  | JSON Text |
|--|--|
|  |{"ok":true,"data":{"assets":{"parcels":[{"id":"51,100","x":51,"y":100,"auction_...|

## 2. Use OpenJSON To Parse the Text

Now that the JSON data is accessible as a string variable, you can use [`OPENJSON`](https://docs.microsoft.com/en-us/sql/t-sql/functions/openjson-transact-sql?view=sql-server-2017) to parse it.

```sql
Select * FROM OPENJSON (@JSON) 
```

By default, `OPENJSON` will return three columns, the key name, the value and the data type for each `{key:value}` pair it finds:

{: .table .table-bordered .table-dark}
| key | value | type |
|--|--|--|
| ok | true | 3 |
| data | {"assets": {parcels":[{"id":"51,100","x":51,"y":100,... | 5 |

The type column will return an int that signifies the `{key:value}` data type:

{: .table .table-bordered .table-striped .width-not-set}
| Type Value | Data Type |
|--|--|
| 0 | null |
| 1 |string |
| 2 | int |
| 3 | true/false |
| 4 | array |
| 5 | object |

## 3. Use Path Variables To Access Nested Data

By default, `OPENJSON` will parse only the root level of the JSON passed to it.
However, most JSON data contains deeply-nested objects. For example, looking at the JSON we are importing, we see that we really just want to import the `parcels` array:

```javascript
{
  "ok": true,
  "data": {
    "assets": {
      "parcels": [{ //This is the array that we want to import
        "id": "51,100",
        "x": 51,
        "y": 100,
        //...more fields...
        }, 
        //...+ 2499 more objects in the parcels array
      ],
      total: 2500
    },
  },
}
```
`OPENJSON` can take a second optional argument as the path to specify for nested objects or arrays. `OPENJSON` will return the `{key:value}` pairs located in this path.
For example, running the following query from our JSON:

```sql
Select * FROM OPENJSON (@JSON, /* optional*/ '$.data') 
```
returns:

{: .table .table-bordered .table-dark}
| key | value | type |
|--|--|--|
| assets | {"parcels":[{"id":"51,100","x":51,"y":100,"auction_price":null,... | 5 |
| total | 2500 | 2 |

The path variable implements the same dot notation that is used in Javascript objects. To select the `parcels` array in our [JSON](#3-use-path-variables-to-access-nested-data), we specify the path:

```sql
SELECT * FROM OPENJSON (@JSON, '$.data.assets.parcels')
```
Because `parcels` is an array, `OPENJSON` will convert all of the array elements to rows:

{: .table .table-bordered .table-dark}
<table>
    <thead>
        <tr>
          <th></th>
            <th>key</th>
            <th>value</th>
            <th>type</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>0</td>
            <td>{"id":"1,1","x":1,"y":1,...</td>
            <td>5</td>
        </tr>
         <tr>
            <td>2</td>
            <td>1</td>
            <td>{"id":"1,2","x":1,"y":2,...</td>
            <td>5</td>
        </tr>
         <tr>
            <td>3</td>
            <td>2</td>
            <td>{"id":"1,3","x":1,"y":3,...</td>
            <td>5</td>
        </tr>
        <tr>
            <td colspan="4">...lines 4 to 2499...</td>
        </tr>
        <tr>
            <td>2500</td>
            <td>2499</td>
            <td>{"id":"50,50","x":50,"y":50,...</td>
            <td>5</td>
        </tr>
    </tbody>
</table>

## 4. Specify an Explicit Schema Using the `WITH` Clause

Now we have a rowset with JSON text for each row that we want to import. How do we generate columns and populate them with the `{key:pair}` data?

`OPENJSON` can be used in conjunction with the `WITH` clause to format the output.[^fn3] Using the `WITH` clause allows you to specify the the data types and (if you want) the columns names of the resulting rowset. 

The `WITH` clause must follow the `OPENJSON` selection. It is passed a set of column names with the path specified:

{: .table .table-dark}
<table>
    <thead>
        <tr>
          <th></th>
            <th>column name</th>
            <th>data-type</th>
            <th>JSON path (optional)</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><pre class="highlight"><code><span class="k">WITH</span></code></pre></td>
            <td><pre class="highlight"><code><span class="p">(</span><span class="n">price</span></code></pre></td>
            <td><pre class="highlight"><code><span class="n">INT</span></code></pre></td>
            <td><pre class="highlight"><code><span class="s1">$.auction_price</span><span class="p">)</span></code></pre></td>
        </tr>
    </tbody>
</table>




By default, `OPENJSON` will match columns names to keys, so it's only necessary to include the JSON path when you wish to choose a column name different to the key name:

```sql
SELECT *
FROM OPENJSON (@JSON, '$.data.assets.parcels') 
WITH (id VARCHAR(7), 
		x INT,
		y INT,
		price INT '$.auction_price') -- new column name
)
```
Only the keys included in the `WITH` clause will be returned:

{: .table .table-bordered .table-dark}
| id | x| y | price |
|--|--|--|--|--|--|
| 51,78 | 51 | 78 | 8790 |
| 51,79 | 51 | 79 |1000 |
| 51,80 | 51 | 80 | 2815 |

## 5. Save the Rowsets into a Table

Now that you've specified the fields you want to extract from the JSON, you can save the rowsets to a table. Here is the full solution:

```sql

DECLARE @JSON VARCHAR(MAX)

SELECT @JSON = BulkColumn
FROM OPENROWSET 
(BULK 'C:\file-location\my-data.json', SINGLE_CLOB) 
AS j

SELECT id, x, y, auction_price, district_id, [owner], [data]
INTO MyTableName
  FROM OPENJSON (@JSON, '$.data.assets.parcels')
  WITH (id VARCHAR(7),
    x INT,
    y INT,
    auction_price INT,
    district_id VARCHAR(50),
    [owner] VARCHAR(50), -- Encase SQL keywords in square brackets
    [data] NVARCHAR(MAX) AS JSON)  -- Specify the JSON format when necessary
```
As noted above in the `[data]` comment, if the property contains an inner JSON object or array, you must append the `AS JSON` option so that it will be imported in JSON format.

## Footnotes

[^fn1]: **CLOB** stands for **C**haracter **L**arge **OB**ject. Depending on your requirements, you can also use:
	- `SINGLE_BLOB`, which reads a file as `varbinary(max)`
	- `SINGLE_NCLOB`, which reads a file as `nvarchar(max)`

[^fn2]: Aka [Decentraland](https://docs.decentraland.org/decentraland/introduction/), an exciting virtual reality platform powered by the Ethereum blockchain. 

[^fn3]: Not to be confused with the [Common Table Expression `WITH` clause](https://docs.microsoft.com/en-us/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-2017#syntax)



