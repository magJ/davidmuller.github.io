---
layout: post
title: How To Use the sqlite3 Module in Python 3
date:  2020-06-02 14:00:00 -0700
categories: posts
---

# {{ page.title }}

<small style="font-weight: 175;">{{ page.date | date: "%-d %B, %Y"}}</small>

*This article was [originally published in DigitalOcean’s public knowledge base](https://www.digitalocean.com/community/tutorials/how-to-use-the-sqlite3-module-in-python-3). It has been reproduced here with some minor edits.*

### Introduction

[SQLite](https://www.sqlite.org/index.html) is a self-contained, file-based SQL database. SQLite comes bundled with [Python](https://www.python.org/) and can be used in any of your Python applications without having to install any additional software.

In this tutorial, we’ll go through the [`sqlite3` module in Python 3](https://docs.python.org/3.8/library/sqlite3.html). We’ll create a connection to a SQLite database, add a table to that database, insert data into that table, and read and modify data in that table.

For this tutorial, we’ll be working primarily with an inventory of fish that we need to modify as fish are added to or removed from a fictional aquarium.

## Prerequisites

To get the most out of this tutorial, it is recommended to have some familiarity with programming in Python and some basic background with SQL.

You can review these tutorials for the necessary background information:

- [How to Code in Python3](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-python-3)
- [An Introduction to Queries in MySQL](https://www.digitalocean.com/community/tutorials/introduction-to-queries-mysql)

## Step 1 — Creating a Connection to a SQLite Database

When we connect to a SQLite database, we are accessing data that ultimately resides in a file on our computer. SQLite databases are fully featured SQL engines that can be used for many purposes. For now, we'll consider a database that tracks the inventory of fish at a fictional aquarium.

We can connect to a SQLite database using the Python `sqlite3` module:

```python
import sqlite3

connection = sqlite3.connect("aquarium.db")
```

`import sqlite3` gives our Python program access to the `sqlite3` module. The `sqlite3.connect()` function returns a [`Connection`](https://docs.python.org/3/library/sqlite3.html#sqlite3.Connection) object that we will use to interact with the SQLite database held in the file `aquarium.db`. The `aquarium.db` file is created automatically by `sqlite3.connect()` if `aquarium.db` does not already exist on our computer.

We can verify we successfully created our `connection` object by running:

```python
print(connection.total_changes)
```

If we run this Python code, we will see output like:

```python
0
```

`connection.total_changes` is the total number of database rows that have been changed by `connection`. Since we have not executed any SQL commands yet, 0 `total_changes` is correct.

If, at any time, we find we want to start this tutorial again, we can delete the `aquarium.db` file from our computer.

> **Note:** It is also possible to connect to a SQLite database that resides strictly in memory (and not in a file) by passing the special string `":memory:"` into `sqlite3.connect()`. For example, `sqlite3.connect(":memory:")`. A `":memory:"` SQLite database will disappear as soon as your Python program exits. This might be convenient if you want a temporary sandbox to try something out in SQLite, and don't need to persist any data after your program exits.

## Step 2 — Adding Data to the SQLite Database

Now that we have connected to the `aquarium.db` SQLite database, we can start inserting and reading data from it.

In a SQL database, data is stored in tables. Tables define a set of columns, and contain 0 or more rows with data for each of the defined columns.

We will create a table named `fish` that tracks the following data:

| name  | species    | tank_number |
|-------|------------|-------------|
| Sammy | shark      | 1           |
| Jamie | cuttlefish | 7           |

The `fish` table will track a value for `name`, `species`, and `tank_number` for each fish at the aquarium. Two example `fish` rows are listed: one row for a `shark` named `Sammy`, and one row for a `cuttlefish` named `Jamie`.

We can create this `fish` table in SQLite using the `connection` we made in Step 1:

```python
cursor = connection.cursor()
cursor.execute("CREATE TABLE fish (name TEXT, species TEXT, tank_number INTEGER)")
```

`connection.cursor()` returns a [`Cursor` object](https://docs.python.org/3/library/sqlite3.html#sqlite3.Cursor). `Cursor` objects allow us to send SQL statements to a SQLite database using `cursor.execute()`. The `"CREATE TABLE fish ..."` string is a SQL statement that creates a table named `fish` with the three columns described earlier: `name` of type `TEXT`, species of type `TEXT`, and `tank_number` of type `INTEGER`.

Now that we have created a table, we can insert rows of data into it:

```python
cursor.execute("INSERT INTO fish VALUES ('Sammy', 'shark', 1)")
cursor.execute("INSERT INTO fish VALUES ('Jamie', 'cuttlefish', 7)")
```

We call `cursor.execute()` two times: once to insert a row for the shark `Sammy` in tank `1`, and once to insert a row for the cuttlefish `Jamie` in tank `7`. `"INSERT INTO fish VALUES ..."` is a SQL statement that allows us to add rows to a table.

In the next section, we will use a SQL `SELECT` statement to inspect the rows we just inserted into our `fish` table.

## Step 3 — Reading Data from the SQLite Database

In Step 2, we added two rows to a SQLite table named `fish`. We can retrieve those rows using a `SELECT` SQL statement:

```python
rows = cursor.execute("SELECT name, species, tank_number FROM fish").fetchall()
print(rows)
```

If we run this code, we will see output like the following:

```python
[('Sammy', 'shark', 1), ('Jamie', 'cuttlefish', 7)]
```

The `cursor.execute()` function runs a `SELECT` statement to retrieve values for the `name`, `species`, and `tank_number` columns in the `fish` table. `fetchall()` retrieves all the results of the `SELECT` statement. When we `print(rows)` we see a list of two tuples. Each tuple has three entries; one entry for each column we selected from the `fish` table. The two tuples have the data we inserted in Step 2: one tuple for `Sammy` the `shark`, and one tuple for `Jamie` the `cuttlefish`.

If we wanted to retrieve rows in the `fish` table that match a specific set of criteria, we can use a `WHERE` clause:

```python
target_fish_name = "Jamie"
rows = cursor.execute(
    "SELECT name, species, tank_number FROM fish WHERE name = ?",
    (target_fish_name,),
).fetchall()
print(rows)
```

If we run this, we will see output like the following:

```python
[('Jamie', 'cuttlefish', 7)]
```

As with the previous example, `cursor.execute(<SQL statment>).fetchall()` allows us to fetch all the results of a `SELECT` statement. The `WHERE` clause in the `SELECT` statement filters for rows where the value of `name` is `target_fish_name`. Notice that we use `?` to substitute our `target_fish_name` variable into the `SELECT` statement. We expect to only match one row, and indeed we only see the row for `Jamie` the `cuttlefish` returned.

> **Warning:** Never use Python string operations to dynamically create a SQL statement string. Using Python string operations to assemble a SQL statement string leaves you vulnerable to [SQL injection attacks](https://en.wikipedia.org/wiki/SQL_injection). SQL injection attacks can be used to steal, alter, or otherwise modify data stored in your database. Always use the `?` placeholder in your SQL statements to dynamically substitute values from your Python program. Pass a tuple of values as the second argument to `Cursor.execute()` to bind your values to the SQL statement. This substitution pattern is demonstrated here and in other parts of this tutorial as well.

## Step 4 — Modifying Data in the SQLite Database

Rows in a SQLite database can be modified using `UPDATE` and `DELETE` SQL statements.

Let's say, for example, that Sammy the shark was moved to tank number 2. We can change Sammy's row in the `fish` table to reflect this change:

```python
new_tank_number = 2
moved_fish_name = "Sammy"
cursor.execute(
    "UPDATE fish SET tank_number = ? WHERE name = ?",
    (new_tank_number, moved_fish_name)
)
```

We issue an `UPDATE` SQL statement to change the `tank_number` of `Sammy` to its new value of `2`. The `WHERE` clause in the `UPDATE` statement ensures we only change the value of `tank_number` if a row has `name = "Sammy"`.

If we run the following `SELECT` statement, we can confirm our update was made correctly:

```python
rows = cursor.execute("SELECT name, species, tank_number FROM fish").fetchall()
print(rows)
```

If we run this, we will see output like the following:

```python
[('Sammy', 'shark', 2), ('Jamie', 'cuttlefish', 7)]
```

Notice that the row for `Sammy` now has the value of `2` for its `tank_number` column.

Let's say that Sammy the shark was released into the wild and no longer held by the aquarium. Since Sammy no longer lives at the aquarium, it would make sense to remove the `Sammy` row from the `fish` table.

Issue a `DELETE` SQL statement to remove a row:

```python
released_fish_name = "Sammy"
cursor.execute(
    "DELETE FROM fish WHERE name = ?",
    (released_fish_name,)
)
```

We issue a `DELETE` SQL statement to remove the row for `Sammy` the `shark`. The `WHERE` clause in the `DELETE` statement ensures we only delete a row if that row has `name = "Sammy"`.

If we run the following `SELECT` statement, we can confirm our deletion was made correctly:

```python
rows = cursor.execute("SELECT name, species, tank_number FROM fish").fetchall()
print(rows)
```

If we run this code, we will see output like the following:

```python
[('Jamie', 'cuttlefish', 7)]
```

Notice that the row for `Sammy` the `shark` is now gone, and only `Jamie` the `cuttlefish` remains.

## Step 5 — Using `with` Statements For Automatic Cleanup

In this tutorial, we've used two primary objects to interact with the `"aquarium.db"` SQLite database: a [`Connection` object](https://docs.python.org/3/library/sqlite3.html#sqlite3.Connection) named `connection`, and a [`Cursor` object](https://docs.python.org/3/library/sqlite3.html#sqlite3.Cursor) named `cursor`.

In the same way that Python files should be closed when we are done working with them, `Connection` and `Cursor` objects should also be closed when they are no longer needed.

We can use a `with` statement to help us automatically close `Connection` and `Cursor` objects:

```python
from contextlib import closing

with closing(sqlite3.connect("aquarium.db")) as connection:
    with closing(connection.cursor()) as cursor:
        rows = cursor.execute("SELECT 1").fetchall()
        print(rows)
```

`closing` is a convenience function provided by the [`contextlib` module](https://docs.python.org/3/library/contextlib.html). When a `with` statement exits, `closing` ensures that `close()` is called on whatever object is passed to it. The `closing` function is used twice in this example. Once to ensure that the `Connection` object returned by `sqlite3.connect()` is automatically closed, and a second time to ensure that the `Cursor` object returned by `connection.cursor()` is automatically closed.

If we run this code, we will see output like the following:

```python
[(1,)]
```

Since `"SELECT 1"` is a SQL statement that always returns a single row with a single column with a value of `1`, it makes sense to see a single tuple with `1` as its only value returned by our code.

## Conclusion

The `sqlite3` module is a powerful part of the Python standard library; it lets us work with a fully featured on-disk SQL database without installing any additional software.

In this tutorial, we learned how to use the `sqlite3` module to connect to a SQLite database, add data to that database, as well as read and modify data in that database. Along the way, we also learned about the risks of SQL injection attacks and how to use [`contextlib.closing`](https://docs.python.org/3.8/library/contextlib.html#contextlib.closing) to automatically call `close()` on Python objects in `with` statements.

*Editor: Kathryn Hancox*
