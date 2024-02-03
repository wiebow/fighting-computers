---
title: Analysis of Archimate models using SQL queries
description: An quick guide to using SQL statements with an Archi HTML report
draft: false
tags:
  - sql
  - archimate-tool
  - shorts
date: 2024-02-02
---
## Introduction

Modeling can provide you with many benefits. Apart from a very specific visual view, a model can also be used for analysis. One of the most basic things I do is find stuff when asked a question, like “how many services do we run with this-or-that characteristic?”

The tool of choice is [Archi](https://www.archimatetool.com/), a fantastic open source project which allows you to create and view models using [Archimate](https://www.opengroup.org/archimate-forum/archimate-overview), an industry standard to model an enterprise. Making a model is one thing, but how can you use it for *analysis* to gain insight in the enterprise? 

You can filter in the Archi tool itself but, as far as I know, you can only filter for the value of ONE property. What if I need more values and properties matched? Using queries is a great way of finding these elements in a big model. You can do this in the HTML report, which is super powerful.

As using the SQL queries in a HTML report is not that well documented, I decided to do a little walk through. Before we start: table and column names are case-sensitive. The commands themselves are not, but they are capitalised here for clarity.

## The Basics

1. How to start: open your model in **Archi**, and select **Tools > Preview HTML report**.
2. Click on the model name in the HTML report, and select the **Query** tab.

Let’s get to know the structure of the database behind your HTML report. First, let’s show all the tables and their names in the model. Enter the following command in the query line and press enter:

```sql
SHOW TABLES
```

Which will give you a list of all the tables in the model.

![[sql01.png]]

Each table in the database has more information in it. To see all the rows and columns (fields) in the **Elements** table you enter:

```sql
SELECT * FROM Elements
```

Which results in:

![[sql02.png]]

Properties for each element are stored in the **Properties** table, so enter this to see them all:

```sql
SELECT * FROM Properties
```


![[sql03.png]]

This is handy but with big models it will present too much information.

## Getting Specific

We need to drill down a bit more: to list all items with a *User Property* named **BIV** we enter:

```sql
SELECT * FROM Properties WHERE propkey = 'BIV'
```

This will show the items in this table with the queried values:

![[sql04.png]]

The *conceptid* is the id of the element that has this user property. Linking the elements and the properties is this unique ID, which is also used in the **Elements** table, as seen above.

So, let’s find the name of the element with this id:

```sql
SELECT name FROM Elements WHERE id = 'id-645f06b9702d4e31885e51cc7e1554d2'
```

![[sql05.png]]

We now know that the Application called ‘Application A’ was the one that was listed in the previous query, because the ID’s matched. This works, but it is cumbersome as the results need to come from different tables.

## Combining Tables

We want to join information from these two tables (_Elements_ and _Properties_) together in one query. We need to match the ids which also (to make things more interesting) have different field names:

![[sql06.png]]

We combined results from two tables in our output. We got the type and name of the element, and the property name and value as well.

As the command is a bit long, it is added here split over several lines for clarity:

```sql
SELECT e.type, e.name, p.propkey, p.propvalue
FROM Elements e JOIN Properties p
ON e.id = p.conceptid
WHERE p.propkey = 'BIV'
```

In essence: we select information, from two tables, when id’s are identical and there is a property called BIV.

The `FROM` and `JOIN` commands introduce a shorthand for the table names, so it is easier to read. The `ON` command makes sure that we only select the items with the same ID (even though they are named differently in each table)

Note: the query is formatted here over a few lines, but it needs to be entered as one line in the query box.

Using variations of this query, we can find other information. Let’s see which Elements have a BIV value of 112 by adding a line starting with AND:

```sql
SELECT e.type, e.name, p.propkey, p.propvalue
FROM Elements e JOIN Properties p
ON e.id = p.conceptid
WHERE p.propkey = 'BIV'
AND p.propvalue = '112'
```

Here is the result:

![[sql07.png]]

## Summary

Now we can start querying our model for Elements that have certain property/value pairs and show us their names or anything else we want to know about them. Examples are: contact names, service levels, product owners; you name it. This makes it possible to analyze models faster and gain insight quickly. Make sure to add the metadata that you are interested in to your elements.

### Further reading

[alasql wiki page](https://alasql-wiki.readthedocs.io/en/latest/readme.html)
