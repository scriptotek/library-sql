---
title: "Introduction to SQL"
teaching: 15
exercises: 0
questions:
- "What is SQL? Why is it useful?"
objectives:
- "to be able to outline what SQL and databases are"
- "open a database with Sqlite3 from the unix shell"
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

1. A `source` folder containing 5 CSV files (comma separated files): `articles.csv`, 
   `journals.csv`, `languages.csv`, `publishers.csv` and `licences.csv`.
2. A bash script: `import.sh`. 

Copy the `source` folder and `import.sh` into the sqlite folder inside the library carpentry folder.

## Import the CSV files into tables in SQLite
In order to work with the data in the CSV files, we have to import them into SQLite 
and in the process turn them into dabase tables. 
But you can usually not directly import CSV files into Sqlite without making some minor changes to them first.
We will do this with OpenRefine with one file to show how it's done manually; and after, we will run a script
to import the rest of the files.

## Readying a CSV file with OpenRefine
Unfortunately, SQLite does not like CSV files that have commas in excess of those that delimit the different columns, and will confuse these for delimiters when they really just are normal commas in senteces. By importing a CSV file that looks like this: 

1,Richard Dawkins,The Blind Watchmaker<br>
2,Douglas Adams,"So long, and thanks for all the fish"<br>
3,"Jane B. Reece, Lisa A. Urry",Campbell Biology<br>

We expect to get a table like this:

| Id  | Name                        | Title                                |
| --- | --------------------------- | ------------------------------------ |
| 1   | Richard Dawkins             | The Blind Watchmaker                 |
| 2   | Douglas Adams               | So long, and thanks for all the fish |
| 3   | Jane B. Reece, Lisa A. Urry | Campbell Biology                     |

But would end up getting this:

| Id  | Name            | Name + Title         | Title + Empty               |
| --- | --------------- | -------------------- | --------------------------- |
| 1   | Richard Dawkins | The Blind Watchmaker |                             |
| 2   | Douglas Adams   | So long              | and thanks for all the fish |
| 3   | Jane B. Reece   | Lisa A. Urry         | Campbell Biology            |

Because SQLite spilts into columns at __ALL__ the commas.
In reality the import will fail, but that is hardly any better!

To solve this, we can use a text refining tool like OpenRefine to turn the CSV file into a format that SQLite can import.

1. Browse to the file `articles.csv` in the `sql-lessons\source` folder and click `Next`.
2. Click `Create Projec` (in the top right).
3. Click `Export` -> `Custom Tabular Exporter` and __uncheck__ `Output column headers`.
4. Go to the `Download` tab and make sure `Tab-separated values (TSV)` is checked.
5. Click `Download`. Go to the Download-folder and copy the file `articles-csv.tsv` to the sqlite `sources` folder.

<!--
//This section should be replaced by the one above, if OpenRefine is not part of the previous session(s)
## Import the CSV files into tables in SQLite
In order to work with the data in the CSV files, we have to import them into SQLite 
and in the process turn them into dabase tables. 
This can be a bit cumbersome, so for the purpose of this course we have made a script
that does this:

1. Make sure you are in the sqlite-folder and that the source folder is also there. 
2. Run import.sh by writing 'bash import.sh in the prompt
3. Then write: ls libcarp.sqlite3 to see that the database was created successfully 
 _(hint: you can use the `tab-key` to autocomplete 'ls l..')_  

## Sqlite3
Sqlite3 is the SQLite version we are using in this course. Let's open `libcarp.sqlite3`,
the newly made database containing the tables made from the 5 CSV-files.
To open a database with SQLite from the unix prompt, write sqlite3 and the name of the database:
-->

## Sqlite3
Sqlite3 is the SQLite version we are using in this course. Let's create and open the database `libcarp.sqlite3`,
We both create and open databases with SQLite from the unix prompt by writing sqlite3 and the name of the database:

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

Notice that the prompt has changed into `sqlite>`. We are no longer in the Bash shell, but in the SQLite3 shell with its own set of commands. We will now use one of these commands to import the newly converted tab separated file `articles-csv.tsv` into a table with the name `articles`. Because we prepared the file for import with OpenRefine, this is now possible.

But first we need to create the table to import the file into.
Paste this into the sqlite-shell and click enter:

> CREATE TABLE articles (id INTEGER, Title TEXT, Authors TEXT, DOI TEXT, URL TEXT, Subjects TEXT, ISSNs TEXT, Citation TEXT, LanguageId INTEGER, LicenceId INTEGER, Author_Count INTEGER, First_Author TEXT, Citation_Count INTEGER, Day INTEGER, Month INTEGER, Year INTEGER);

You have now created the table `articles`, which we will import the content of the CSV-file into.
To see that this table was created, you can type:

~~~
.tables
~~~
{: .sql}

To import the contents of  `articles-csv.tsv` into the table `articles`, type these two commands, pressing the enter key for each one:

~~~
.separator \t
.import sources/articles-csv.tsv articles
~~~
{: .sql}

These commands do two things:

1. `.separator \t` sets the delimiter, or the separator, between the columns to be the `tab` character (\t). 
   We need to do this since the default separator of Sqlite is '|'
2. `.import sources/articles-csv.tsv articles` imports the file `articles-csv.tsv` in the folder `sources` into the table `articles`.

<!--
//Replace this with the above lines, if you are only using the script to import the tables
This shell has its own set of commands. For example, in order to see which tables you have in your databatase you can type:
-->

We still have four other files we need to prepare with OpenRefine and make tables to import into - but this is a lot of work!
Luckily there is also another option, and that is to use a script to do this for us.
Sometimes taking the time to write a script can save a lot of tedious work.

To run this script, we need to exit Sqlite and go back to the bash shell. 
You can type either:
`.quit` or `.q`, or `.exit` or `.e`.

Once you are back in the bash shell, make sure you are in the sqlite folder, and write
> $ `bash import.sh`

`import.sh` is the script, and bash is what you write infront of it to tell the shell to execute it.

When the script is finished, go back into Sqlite3 by typing:

~~~
$ sqlite3 libcarp.sqlite3
~~~
{: .sql}

To see that the new tables were made, you can again type:

~~~
.tables
~~~
{: .sql}

And the tables `articles` `journals` `languages` `publishers` `licences` will be listed

To list all the available commands in SQlite, you can type `.help` or `.h`.
To exit SQLite and return to the shell command line, you can use either
`.quit` or `.q`, or `.exit` or `.e`.
