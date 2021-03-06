---
title: "Basic queries"
teaching: 25
exercises: 20
questions:
- "How do you query databases using SQL?"
objectives:
- "understand how SQL can be used to query databases"
keypoints:
- "SQL is ideal for querying databases"
---
## Writing my first query

Let's start by using the __articles__ table. Here we have data on every
article that has been published, including the title of the article, the
authors, date of publication, etc.

Let’s write an SQL query that selects only the title column from the
articles table.

~~~
SELECT title FROM articles;
~~~
{: .sql}

We have capitalized the words SELECT and FROM because they are SQL keywords.
SQL is case-insensitive, but it helps for readability, and is good style.
Also note that SQLite demands queries to be terminated by a semicolon.

If we want more information, we can add new columns to the list of fields,
right after `SELECT`, separating with comma:

~~~
SELECT title, authors, issns FROM articles;
~~~
{: .sql}

You can change some SQLite settings to make the output easier to read.
First, set the output mode to display left-aligned columns. 
Then turn on the display of column headers.

~~~
.mode column
.header on
~~~
{: .sql}

Note that these commands (like `.quit`) are not terminated by a semicolon, in fact they __wont__ work
if you put a semicolon at the end. __Rule:__ If it starts with a period, it must not end with semicolon!

Let's try and see how `.mode column` and `.header on` have affected our output, 
by re-running the query from above - but now let's add a `LIMIT` clause:

~~~
SELECT title, authors, issns FROM articles LIMIT 10;
~~~
{: .sql}

The `LIMIT 10` clause will show only the 10 first rows in the table.

We can now see that `.header on` shows a header row, and that `.mode column` snugly lines up the 
columns making them much easier to read. The drawback with `.mode column` is that only a limited width of 
the columns are shown, cutting off the title and authors.

You can change the width of the columns with `.width` like this:

~~~
.width 60 10
~~~
{: .sql}

The `.width 60 10` tells the Sqlite to show the first 60 characters of the `title` column 
and the first 10 characters of the `authors` column. If you have more columns, add more numbers.
To reset the width settings, you can use `.width auto`.

To select __all__ of the columns in a table using the wildcard '*'

~~~
SELECT * FROM articles;
~~~
{: .sql}

This will often give a messy result in Sqlite depending on the number and size of columns, so often it's
better to be more specific when you select columns.
If you work with a table you haven't made yourself (like in this case), you will not know the number of columns, 
or their names or data types. To list the columns of a table you can use `PRAGMA table_info();`

~~~
PRAGMA table_info(articles);
~~~
{: .sql}

The most useful for you in this course, will be the name and the type columns.

&nbsp;

> ## Challenge
>
> Do a `PRAGMA table_info(articles)` and choose a couple of columns you want to look at, 
> use the LIMIT clause and test different values of `.width`.
{: .challenge}

## Unique values

If we want to avoid seeing duplicate entries in a column we use `DISTINCT`.
For example, if we want a list of all the ISSNs in the `articles` table, 
but we know that some articles are published in the same journal
and we don't want duplicates, we can use this query:

~~~
SELECT DISTINCT issns FROM articles;
~~~
{: .sql}

If we select more than one column, then the distinct __combinations__ of values are returned:

~~~
SELECT DISTINCT issns, day, month, year FROM articles;
~~~
{: .sql}

## Calculated values

We can also do calculations with the values in a query.
For example, if we wanted to look at the relative popularity of an article,
we divide by 10 (because we know the most popular article has 10 citations).

~~~
SELECT first_author, citation_count/10.0 FROM articles;
~~~
{: .sql}

When we run the query, the expression `citation_count / 10.0` is evaluated for each
row and appended to that row, in a new column.  Expressions can use any fields,
any arithmetic operators (`+`, `-`, `*`, and `/`) and a variety of built-in
functions. For example, we could round the values to make them easier to read.

~~~
SELECT first_author, title, ROUND(author_count/16.0, 2) FROM articles;
~~~
{: .sql}

> Note that we divide by `10.0` and `16.0` instead of `10` and `16` to avoid losing the remainder.
> In SQLite, if you divide an integer by an integer, you get an integer, removing everything behind
> the decimal, making 9/10 = 0 instead of 0.9.

&nbsp;

> ## Challenge
>
> Write a query that returns a unique list of first authors
{: .challenge}

## Filtering

Databases can also filter data – selecting only the data meeting certain
criteria.  For example, let’s say we only want data for a specific ISSN
for the journal _International Journal of Molecular Sciences_,
which has a ISSN code 1422-0067.  We need to add a `WHERE` clause to our query:

~~~
.width 50 20
SELECT title, authors FROM articles WHERE issns='1422-0067';
~~~
{: .sql}


We can use more sophisticated conditions by combining tests with `AND` and `OR`.
For example, suppose we want list the articles in _International Journal of Molecular Sciences_ that has two or less authors:

~~~
SELECT title, authors,author_count FROM articles WHERE (issns='1422-0067') AND (author_count <=2);
~~~
{: .sql}

Note that the parentheses are not needed in the query above, but they help with
readability. In queries combining `AND` and `OR`, they ensure that we get the result 
we intend.

If we wanted to get data for the *Humanities* and *Religions* journals, which have
ISSNs codes `2076-0787` and `2077-1444`, we could combine the tests using OR:

~~~
SELECT * FROM articles WHERE (issns = '2076-0787') OR (issns = '2077-1444');
~~~
{: .sql}

> ## Challenge
>
> Write a query that returns the title, first_author, issns, month and year
> for all single author papers with more than 4 citations
{: .challenge}


## Building more complex queries

Now, lets combine the above queries to get data for the 3 journals from
June on.  This time, let’s use IN as one way to make the query easier
to understand.  It is equivalent to saying `WHERE (issns = '2076-0787') OR (issns
= '2077-1444') OR (issns = '1422-0067')`, but reads more neatly:

~~~
SELECT title,citation,author_count FROM articles WHERE (author_count>=3) AND (issns IN ('2076-0787', '2077-1444', '1422-0067'));
~~~
{: .sql}

We started with something simple, then added more clauses one by one, testing
their effects as we went along. For complex queries, this is a good strategy,
to make sure you are getting what you want. Sometimes it might help to take a
subset of the data that you can easily see in a temporary database to practice
your queries on before working on a larger or more complicated database.

But when the queries become more complex, it can be useful to make them more readable
by using the IN operator.

## Sorting

We can also sort the results of our queries by using `ORDER BY`.
For simplicity, let’s go back to the articles table and alphabetize it by issns.

~~~
.width 20 40 10
SELECT first_author, title, issns FROM articles ORDER BY issns ASC;
~~~
{: .sql}

The keyword `ASC` tells us to order it in Ascending order.
We could alternately use `DESC` to get descending order.

~~~
SELECT first_author, title FROM articles ORDER BY first_author DESC;
~~~
{: .sql}

`ASC` is the default.

We can also sort on several fields at once, for example first order by `issns` then `first_authors`.

~~~
SELECT first_author, title, issns FROM articles ORDER BY issns DESC, first_author ASC;
~~~
{: .sql}

> ## Challenge
>
> Write a query that returns title, first_author, issns and citation_count from
> the articles table, sorted with the most cited article at the top and
> alphabetically
{: .challenge}

## Order of execution

Another note for ordering. We don’t actually have to display a column to sort by
it.  For example, let’s say we want to order the first authors that published in november
by their citation count, but not show the citation count:

~~~
SELECT first_author FROM articles WHERE month='11' ORDER BY citation_count DESC;
~~~
{: .sql}

We can do this because sorting occurs earlier in the computational pipeline than
field selection.

The computer is basically doing this:

1. Filtering rows according to WHERE
2. Sorting results according to ORDER BY
3. Displaying requested columns or expressions.

> Clauses are written in a fixed order: `SELECT`, `FROM`, `WHERE`, then `ORDER BY`.

<!-- -->

 It is possible to write a query placing each clause on its own line, we recommend this for readibility.
 
> ## Challenge
>
> Let's try to combine what we've learned so far in a single
> query.  Using the `articles` table, write a query to display the three date fields,
> `issn`, and `citation_count`, for articles published after June, ordered
> alphabetically by first author name. Write the query as a single line, then
> put each clause on its own line, and see how more readible the query becomes!
{: .challenge}
