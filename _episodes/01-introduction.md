---
title: "Introduction to SQL"
teaching: 15
exercises: 0
questions:
- "What is SQL? Why is it useful?"
objectives:
- "to be able to outline what SQL and databases are"
- "open a database with Sqlite3 from the unix shell
keypoints:
- "SQL (Structured Query Language) is a query language used to interact with databases"
- "Databases contain data organized in tables"
- "SQLite is a database management system"
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
1. Download a zip file with the dataset from
    [here](https://github.com/uio-carpentry/2017-02-02-librarycarpentry/raw/gh-pages/data/sqlite-lesson.zip).
2. Unzip the file on your Desktop.

The sqlite-lesson folder contains:

1. A `source` folder containing 5 CSV files (comma separated files): `articles.csv`, `journals.csv`, `languages.csv`, `publishers.csv` and `licences.csv`.
2. A bash script: `import.sh`. 

Copy the `source` folder and `import.sh` into the sqlite folder inside the library carpentry folder.

## Import the CSV files into tables in SQLite
In order to work with the data in the CSV files, we have to import them into SQLite 
and in the process turn them into dabase tables. 
But you can usually not directly import CSV files into Sqlite without making some minor changes to them first.
We will do this with OpenRefine with one file to show how it's done manually; and after, we will run a script
to import the rest of the files.

## Readying a CSV file with OpenRefine
1. Browse to the file `articles.csv` in the `sql-lessons\source` folder and click `Next`.
2. Click `Create Projec` (in the top right).
3. Click `Export` -> `Custom Tabular Exporter` and __uncheck__ `Output column headers`.
4. Go to the `Download` tab and make sure `Tab-separated values (TSV)` is checked
5. Click `Download`. Go to the Download-folder and copy the file `articles.csv.tsv` to the sqlite `sources` folder

<!--
//This section should be used if OpenRefine is not part of the previous session(s)

This can be a bit cumbersome, so for the purpose of this course we have made a script
that does this:

1. Make sure you are in the sqlite-folder and that the source folder is also there. 
2. Run import.sh by writing 'bash import.sh in the prompt
3. Then write: ls libcarp.sqlite3 to see that the database was created successfully 
 _(hint: you can use the `tab-key` to autocomplete 'ls l..')_  
-->
## Sqlite3
Sqlite3 is the SQLite version we are using in this course. Let's open `libcarp.sqlite3`,
the newly made database containing the tables made from the 5 CSV-files.
To open a database with SQLite from the unix prompt, write sqlite3 and the name of the database:

~~~
$ sqlite3 libcarp.sqlite3
~~~
{: .sql}

You should then see some info and a new prompt looking something like this:

~~~
SQLite version 3.6.20
Enter ".help" for instructions
Enter SQL statements terminated with a ";"
sqlite>
~~~
{: .sql}

Notice that the prompt has changed into `sqlite>`. We are no longer in the Bash shell, but in the SQLite3 shell. 
This shell has its own set of commands. For example, in order to see which tables you have in your databatase you can type:

~~~
.tables
~~~
{: .sql}

And the tables  `articles.csv` `journals.csv` `languages.csv` `publishers.csv` `licences.csv` will be listed

To list all the available commands in SQlite, you can type `.help` or `.h`.
To exit SQLite and return to the shell command line, you can use either
`.quit` or `.q`, or `.exit` or `.e`.
