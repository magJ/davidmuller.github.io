---
layout: post
title: How To Use the collections Module in Python 3
date:  2020-05-08 14:00:00 -0700
categories: posts
---

# {{ page.title }}

<small style="font-weight: 175;">{{ page.date | date: "%-d %B, %Y"}}</small>

*This article was [originally published in DigitalOcean’s public knowledge base](https://www.digitalocean.com/community/tutorials/how-to-use-the-collections-module-in-python-3). It has been reproduced here with some minor edits.*

### Introduction

Python 3 has a number of built-in data structures, including tuples, dictionaries, and lists. Data structures provide us with a way to organize and store data. The `collections` module helps us populate and manipulate data structures efficiently.

In this tutorial, we’ll go through three classes in [the `collections` module](https://docs.python.org/3/library/collections.html#module-collections) to help you work with tuples, dictionaries, and lists. We’ll use `namedtuples` to create tuples with named fields, `defaultdict` to concisely group information in dictionaries, and `deque` to efficiently add elements to either side of a list-like object.

For this tutorial, we’ll be working primarily with an inventory of fish that we need to modify as fish are added to or removed from a fictional aquarium.

## Prerequisites

To get the most out of this tutorial, it is recommended to have some familiarity with the tuple, dictionary, and list data types, both with their syntax, and how to retrieve data from them. You can review these tutorials for the necessary background information:

- [Understanding Tuples in Python 3](https://www.digitalocean.com/community/tutorials/understanding-tuples-in-python-3)
- [Understanding Dictionaries in Python 3](https://www.digitalocean.com/community/tutorials/understanding-dictionaries-in-python-3)
- [Understanding Lists in Python 3](https://www.digitalocean.com/community/tutorials/understanding-lists-in-python-3)

## Adding Named Fields to Tuples

Python tuples are an immutable, or unchangeable, ordered sequence of elements. Tuples are frequently used to represent columnar data; for example, lines from a CSV file or rows from a SQL database. An aquarium might keep track of its inventory of fish as a series of tuples.

An individual fish tuple:

```python
("Sammy", "shark", "tank-a")
```

This tuple is composed of three string elements.

While useful in some ways, this tuple does not clearly indicate what each of its fields represents. In actuality, element `0` is a name, element `1` is a species, and element `2` is the holding tank.

Explanation of fish tuple fields:

| name  | species | tank   |
|-------|---------|--------|
| Sammy | shark   | tank-a |

This table makes it clear that each of the tuple's three elements has a clear meaning.

`namedtuple` from the `collections` module lets you add explicit names to each element of a tuple to make these meanings clear in your Python program.

Let's use `namedtuple` to generate a class that clearly names each element of the fish tuple:


```python
from collections import namedtuple

Fish = namedtuple("Fish", ["name", "species", "tank"])
```

`from collections import namedtuple` gives your Python program access to the `namedtuple` factory function. The `namedtuple()` function call returns a class that is bound to the name `Fish`. The `namedtuple()` function has two arguments: the desired name of our new class `"Fish"` and a list of named elements `["name", "species", "tank"]`.

We can use the `Fish` class to represent the fish tuple from earlier:

```python
sammy = Fish("Sammy", "shark", "tank-a")

print(sammy)
```

If we run this code, we'll see the following output:

```python
Fish(name='Sammy', species='shark', tank='tank-a')
```

`sammy` is instantiated using the `Fish` class. `sammy` is a tuple with three clearly named elements.

`sammy`'s fields can be accessed by their name or with a traditional tuple index:

```python
print(sammy.species)
print(sammy[1])
```
If we run these two `print` calls, we'll see the following output:

```python
shark
shark
```

Accessing `.species` returns the same value as accessing the second element of `sammy` using `[1]`.

Using `namedtuple` from the `collections` module makes your program more readable while maintaining the important properties of a tuple (that they're immutable and ordered).

In addition, the `namedtuple` factory function adds several extra methods to instances of `Fish`.

Use [`._asdict()`](https://docs.python.org/3/library/collections.html#collections.somenamedtuple._asdict) to convert an instance to a dictionary:

```python
print(sammy._asdict())
```

If we run `print`, you'll see output like the following:

```python
{'name': 'Sammy', 'species': 'shark', 'tank': 'tank-a'}
```
Calling `.asdict()` on `sammy` returns a dictionary mapping each of the three field names to their corresponding values.

Python versions older than 3.8 might output this line slightly differently. You might, for example, see an `OrderedDict` instead of the plain dictionary shown here.

> **Note:** In Python, methods with leading underscores are usually considered "private." [Additional methods provided by `namedtuple`](https://docs.python.org/3/library/collections.html#collections.namedtuple) (like `_asdict()`, `._make()`, .`_replace()`, etc.), however, [are public](https://softwareengineering.stackexchange.com/a/391725).

## Collecting Data in a Dictionary

It is often useful to collect data in Python dictionaries. `defaultdict` from the `collections` module can help us assemble information in dictionaries quickly and concisely.

`defaultdict` never raises a `KeyError`. If a key isn’t present, `defaultdict` just inserts and returns a placeholder value instead:

```python
from collections import defaultdict

my_defaultdict = defaultdict(list)

print(my_defaultdict["missing"])
```

If we run this code, we'll see output like the following:

```python
[]
```
`defaultdict` inserts and returns a placeholder value instead of raising a `KeyError`. In this case we specified the placeholder value as a list.

Regular dictionaries, in contrast, will raise a `KeyError` on missing keys:

```python
my_regular_dict = {}

my_regular_dict["missing"]
```
If we run this code, we'll see output like the following:

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'missing'
```
The regular dictionary `my_regular_dict` raises a `KeyError` when we try to access a key that is not present.

`defaultdict` behaves differently than a regular dictionary. Instead of raising a `KeyError` on a missing key, `defaultdict` calls the placeholder value with no arguments to create a new object. In this case `list()` to create an empty list.

Continuing with our fictional aquarium example, let's say we have a list of fish tuples representing an aquarium's inventory:

```python
fish_inventory = [
    ("Sammy", "shark", "tank-a"),
    ("Jamie", "cuttlefish", "tank-b"),
    ("Mary", "squid", "tank-a"),
]
```

Three fish exist in the aquarium—their name, species, and holding tank are noted in these three tuples.

Our goal is to organize our inventory by tank—we want to know the list of fish present in each tank. In other words, we want a dictionary that maps `"tank-a"` to `["Sammy", "Mary"]` and `"tank-b"` to `["Jamie"]`.

We can use `defaultdict` to group fish by tank:

```python
from collections import defaultdict

fish_inventory = [
    ("Sammy", "shark", "tank-a"),
    ("Jamie", "cuttlefish", "tank-b"),
    ("Mary", "squid", "tank-a"),
]
fish_names_by_tank = defaultdict(list)
for name, species, tank in fish_inventory:
    fish_names_by_tank[tank].append(name)

print(fish_names_by_tank)
```

Running this code, we'll see the following output:

```
defaultdict(<class 'list'>, {'tank-a': ['Sammy', 'Mary'], 'tank-b': ['Jamie']})
```

`fish_names_by_tank` is declared as a `defaultdict` that defaults to inserting `list()` instead of raising a `KeyError`. Since this guarantees that every key in `fish_names_by_tank` will point to a `list`,  we can freely call `.append()` to add names to each tank's list.

`defaultdict` helps you here because it reduces the chance of unexpected `KeyErrors`. Reducing the unexpected `KeyErrors` means your program can be written more clearly and with fewer lines. More concretely, the `defaultdict` idiom lets you avoid manually instantiating an empty list for every tank.

Without `defaultdict`, the `for` loop body might have looked more like this:

```python
# More Verbose Example Without defaultdict

fish_names_by_tank = {}
for name, species, tank in fish_inventory:
    if tank not in fish_names_by_tank:
      fish_names_by_tank[tank] = []
    fish_names_by_tank[tank].append(name)
```

Using just a regular dictionary (instead of a `defaultdict`) means that the `for` loop body always has to check for the existence of the given `tank` in `fish_names_by_tank`. Only after we've verified that `tank` is already present in `fish_names_by_tank`, or has just been initialized with a `[]`, can we append the fish name.

`defaultdict` can help cut down on boilerplate code when filling up dictionaries because it never raises a `KeyError`.

## Using deque to Efficiently Add Elements to Either Side of a Collection

Python lists are a mutable, or changeable, ordered sequence of elements. Python can append to lists in constant time (the length of the list has no effect on the time it takes to append), but inserting at the beginning of a list can be slower—the time it takes increases as the list gets bigger.

In terms of [Big O notation](https://en.wikipedia.org/wiki/Big_O_notation), appending to a list is a constant time `O(1)` operation. Inserting at the beginning of a list, in contrast, is slower with `O(n)` performance.

> **Note:** Software engineers often measure the performance of procedures using something called "Big O" notation. When the size of an input has no effect on the time it takes to perform a procedure, it is said to run in constant time or `O(1)` ("Big O of 1"). As you learned above, Python can append to lists with constant time performance, otherwise known as `O(1)`.
>
> Sometimes, the size of an input directly affects the amount of time it takes to run a procedure. Inserting at the beginning of a Python list, for example, runs slower the more elements there are in the list. Big O notation uses the letter `n` to represent the size of the input. Adding items to the beginning of a Python list runs in "linear time" or `O(n)` ("Big O of n").
>
> In general, `O(1)` procedures are faster than `O(n)` procedures.


We can insert at the beginning of a Python list:

```python
favorite_fish_list = ["Sammy", "Jamie", "Mary"]

# O(n) performance
favorite_fish_list.insert(0, "Alice")

print(favorite_fish_list)
```
If we run the following, we will see output like the following:

```python
['Alice', 'Sammy', 'Jamie', 'Mary']
```

The `.insert(index, object)` method on list allows us to insert `"Alice"` at the beginning of `favorite_fish_list`. Notably, though, inserting at the beginning of a list has `O(n)` performance. As the length of `favorite_fish_list` grows, the time to insert a fish at the beginning of the list will grow proportionally and take longer and longer.

`deque` (pronounced "deck") from the `collections` module is a list-like object that allows us to insert items at the beginning or end of a sequence with constant time (`O(1)`) performance.

Insert an item at the beginning of a `deque`:

```python
from collections import deque

favorite_fish_deque = deque(["Sammy", "Jamie", "Mary"])

# O(1) performance
favorite_fish_deque.appendleft("Alice")

print(favorite_fish_deque)
```

Running this code, we will see the following output:

```python
deque(['Alice', 'Sammy', 'Jamie', 'Mary'])
```
We can instantiate a `deque` using a preexisting collection of elements, in this case a list of three favorite fish names. Calling `favorite_fish_deque`'s `appendleft` method allows us to insert an item at the beginning of our collection with `O(1)` performance. `O(1)` performance means that the time it takes to add an item to the beginning of `favorite_fish_deque` will not grow even if `favorite_fish_deque` has thousands or millions of elements.

> **Note:** Although `deque` adds entries to the beginning of a sequence more efficiently than a list, `deque` does not perform all of its operations more efficiently than a list. For example, accessing a random item in a `deque` has `O(n)` performance, but accessing a random item in a list has `O(1)` performance. Use `deque` when it is important to insert or remove elements from either side of your collection quickly. A full comparison of time performance is available [on Python's wiki](https://wiki.python.org/moin/TimeComplexity).

## Conclusion

The `collections` module is a powerful part of the Python standard library that lets you work with data concisely and efficiently. This tutorial covered three of the classes provided by the `collections` module including `namedtuple`, `defaultdict`, and `deque`.

From here, you can use [the `collection` module's documentation](https://docs.python.org/3/library/collections.html#module-collections) to learn more about other available classes and utilities. To learn more about Python in general, you can read our [How To Code in Python 3 tutorial series](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-python-3).

*Editor: Kathryn Hancox*
