---
title: "Aggregation"
teaching: 30
exercises: 30
questions:
- "How do you aggregate records in SQL?"
objectives:
- "to understand how to aggregate records in SQL"
keypoints:
- "SQL is ideal for aggregating database records"
---

## `COUNT` and `GROUP BY`

Aggregation allows us to group records, and do counts and calculations.

Let’s go to the articles table and find out how many entries there are.
Using the wildcard simply counts the number of records (rows)

~~~
SELECT COUNT(*) FROM articles;
~~~
{: .sql}

We can also find out how many authors have participated in these articles.

~~~
SELECT COUNT(*), SUM(author_count) FROM articles;
~~~
{: .sql}

There are many other aggregate functions included in SQL including
`MAX`, `MIN`, and `AVG`.

> ## Challenge
>
> Write a query that returns: total, average, the min and max number of
> citations for each journal. Can you modify it so that it outputs only
> journals with more than 5 citations in average?
{: .challenge}

Now, let's see how many articles were published in each journal. We do this
using a `GROUP BY` clause

~~~
SELECT issns, COUNT( * ) FROM articles GROUP BY issns;
~~~
{: .sql}

`GROUP BY` tells SQL what field or fields we want to use to aggregate the data.
If we want to group by multiple fields, we give `GROUP BY` a comma separated list.

> ## Challenge
>
> Write queries that return:
>
> 1. How many articles were counted each month.
a) in total;
b) per each journal.
> 2. Average number of citations of each journal in each month.
Can you modify the above queries combining them into one?
{: .challenge}

## The `HAVING` keyword

In the previous lesson, we have seen the keywords `WHERE`, allowing to
filter the results according to some criteria. SQL offers a mechanism to
filter the results based on aggregate functions, through the `HAVING` keyword.

For example, we can adapt the last request we wrote to only return information
about articles with a 10 or more published articles:

~~~
SELECT issns, COUNT( * ) FROM articles GROUP BY issns HAVING COUNT( * ) >= 10;
~~~
{: .sql}

The `HAVING` keyword works exactly like the `WHERE` keyword, but it is used
with aggregate functions instead of database fields.

If you use `AS` in your query to rename a column, `HAVING` can use this
information to make the query more readable. For example, in the above
query, we can call the `COUNT(*)` by another name, like
`occurrences`. This can be written this way:

~~~
SELECT issns, COUNT( * ) AS Occurrences FROM articles GROUP BY issns HAVING occurrences >= 10;
~~~
{: .sql}

Note that in both queries, `HAVING` comes _after_ `GROUP BY`. One way to
think about this is: the data are retrieved (`SELECT`), can be filtered
(`WHERE`), then joined in groups (`GROUP BY`); finally, we only select some
of these groups (`HAVING`).

> ## Challenge
>
> Write a query that returns, from the `articles` table, the average number of
> citations for each journal, only for the journals with 5 or more citations
> on average.
{: .challenge}

## Ordering aggregated results.

We can order the results of our aggregation by a specific column, including
the aggregated column.  Let’s count the number of articles published in each
journal, ordered by the count

~~~
SELECT issns, COUNT( * ) FROM articles GROUP BY issns ORDER BY COUNT( * ) DESC;
~~~
{: .sql}

## Saving queries for future use

It is not uncommon to repeat the same operation more than once, for example
for monitoring or reporting purposes. SQL comes with a very powerful mechanism
to do this: views. Views are a form of query that is saved in the database,
and can be used to look at, filter, and even update information. One way to
think of views is as a table, that can read, aggregate, and filter information
from several places before showing it to you.

Creating a view from a query requires to add `CREATE VIEW viewname AS`
before the query itself. For example, if we want to save the query giving
the number of journals in a view, we can write

~~~
CREATE VIEW journal_counts AS SELECT issns, COUNT( * ) FROM articles GROUP BY issns;
~~~
{: .sql}

Now, we will be able to access these results with a much shorter notation:

~~~
SELECT * FROM journal_counts;
~~~
{: .sql}

Assuming we do not need this view anymore, we can remove it from the database:

~~~
DROP VIEW journal_counts;
~~~
{: .sql}


> ## Challenge
>
> Write a query that returns the number of articles published in each journal
> on each month, sorted from most popular journal to the ones with least
> publications each month starting from the most recent records. Save this
> query as a `VIEW`.
{: .challenge}
