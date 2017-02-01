---
title: "Introduction to SQL"
teaching: 15
exercises: 0
questions:
- "What is SQL? Why is it useful?"
objectives:
- "to be able to outline what SQL and databases are"
keypoints:
- "SQL (Structured Query Language) is a query language used to interact with databases"
- "Databases contain data organized in tables"
---

## What is SQL?

SQL (Structured Query Language) is a specialized language used to interact with relational databases. 
There are several different kinds of SQL (Sqlite, MySQL, Oracle, Postgres, etc), but all support the same basic statements that we will be covering today.

## Relational databases

Relational databases consist of one or more tables of data.
These tables have _fields_ (columns) and _records_ (rows).

| id  | name   | income  |
| --- | ------ | ------- |
| 1   | Peter  | 543,554 |
| 2   | Ann    | 435,233 |
| 3   | Sophie | 339,457 |

Every field has a data _type_. Every value in the same field of each record has the same _type_. 

| id (integer) | name (text) | income (decimal) |
| ------------ | ----------- | ---------------- |
| 1            | Peter       | 543,554          |
| 2            | Ann         | 435,233          |
| 3            | Sophie      | 339,457          |

Here we see three common data _types_ in Sqlite: `Integer`, `Text` and `Decimal`, but there are many others.


## Why use SQL
SQL is optimised for fast and efficient handling of large amounts of data; and greatly help in 
organizing and making sense out of data. The data types defining the tables both explain what 
kind of data is in a column and put constraints on what can be placed there. The data types 
help with quality control of entries - you will receive an error if you try to input text 
into a field that should contain numbers. Also, if you already know a programming language like
R, Python, PHP or others, these can aid you in using SQL to place your data in a database.

Having data in databases keeps the data separate from analysis and presentation (design).
This is useful since there are many tools and programs that can present data.
If you mix data and design it might be difficult to separate the two later on.
If data and design are kept separate, several platforms (ie mobile apps, web pages, etc)
can use the data at the same time. When the data is updated this can immediately be shown on
all the different platforms.

## Why is SQL well suited for librarians?
SQL is a dedicated language for querying information from datasets and librarianship 
is about information management. We help sort and organise information and we help 
people find information. Most of us use queries to help people find the information 
they need e.g. conducting a search via a library catalogue.
With SQL, you can directly construct your database queries without the constraints 
imposed by a search interface. Librarians are good at  searching for information 
so don’t be afraid – constructing queries using SQL is simply a different and more direct
way of finding information. 

## Database Management Systems
There are a number of different database management systems for working with
relational data. We're going to use SQLite today, but basically everything we
teach you will apply to the other database systems as well. 

## Dataset Description
The data we will be using is a catalogue of journal articles from 51 different
journals published during 2015.


## Download dataset
1. Download the CSV (Comma separated) files from
    [Figshare](https://dx.doi.org/10.6084/m9.figshare.3409471)
2. Create a folder 'sources' in the sqlite folder
3. Unzip and extract the files you downloaded into the sources-folder
4. There are several CSV-files, but we will be using articles.csv and journals.csv

## Import CSV-files into tables in sqlite
In order to work with the data in the CSV-files, we have to import them into SQlite 
and in the process turn them into dabase tables.
This can be a bit cumbersome, so for the purpose of this course we have made a script
that does this:

1. Download the script import.sh from here :xxxxxxxxxxxxxxxxxxxx, and place it in the sqlite-folder
2. Run import.sh by writing 'bash import.sh in the unix prompt
3. Then write: ls libcarp.sqlite3 to see that the database was created successfully 
 _(hint: you can use the `tab-key` to autocomplete 'ls l..')_  

##Sqlite3
Sqlite3 is the SQLite version we are using in this course. Let's open `libcarp.sqlite3`,
the newly made database containing the tables `articles` and `journals` made from the CSV-files.
To open a database with SQLite from the unix prompt, write sqlite3 and the name of the database:
~~~
$ sqlite3 libcarp.sqlite3
~~~
{: .sql}

You should then see a new prompt looking like this `sqlite>`
