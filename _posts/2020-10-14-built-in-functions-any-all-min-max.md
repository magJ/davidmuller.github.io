---
layout: post
title: How To Use the all, any, max, and min Built-In Functions in Python
date:  2020-10-14 14:00:00 -0700
categories: posts
---

# {{ page.title }}

<small style="font-weight: 175;">{{ page.date | date: "%-d %B, %Y"}}</small>

*This article was [originally published in DigitalOcean’s public knowledge base](https://www.digitalocean.com/community/tutorials/how-to-use-built-in-functions-all-any-max-and-min-in-python). It has been reproduced here with some minor edits.*

### Introduction

Python includes a number of [built-in functions](https://docs.python.org/3/library/functions.html)—these are global Python functions that can be called from any Python code without importing additional modules. For example, you can always call the `print` built-in function to output text.

Several of the built-in functions (`all`, `any`, `max`, and `min` among them) take _iterables_ of values as their argument and return a single value. Built-in functions are useful when, for example, you want to determine if all or any of the elements in a list meet a certain criteria, or find the largest or smallest element in a list.

In this tutorial, you will use the built-in functions `all`, `any`, `max`, and `min`.

## Using `all`

The built-in function `all` checks if every element in an iterable is `True`. For example:

```python
all([True, True])
```

If you run the previous code, you will receive this output:

```
True
```

In this first example, you call `all` and give it a list with two elements (two `True` Boolean values). Since every element in the iterable is `True`, the output was `True`.

`all` will return `False` if one or more elements in the given iterable is `False`:

```python
all([True, False, True])
```

If you run the previous code, you'll receive this output:

```
False
```

You call `all` with a list containing three elements including one `False` Boolean value. Since one of the elements in the iterable was `False`, the output of the call to `all` was `False`.

Notably, `all` stops iterating and immediately returns `False` as soon as it encounters the first `False` entry in an iterable. So, `all` can be useful if you want to check successive conditions that may build on each other, but return immediately as soon as one condition is `False`.

A special case to be aware of is when `all` is given an empty iterable:

```python
all([])
```

If you run the previous code, you'll receive this output:

```
True
```

When you give `all` an empty iterable (for example, an empty list like `all([])`), its return value is `True`. So, `all` returns `True` if every element in the iterable is True or there are 0 elements.

`all` is particularly helpful when combined with generators and custom conditions. Using `all` is often shorter and more concise than if you were to write a full-fledged `for` loop. Let's consider an example to find out whether there are elements in a list that start with `"s"`:

```python
animals = ["shark", "seal", "sea urchin"]
all(a.startswith("s") for a in animals)
```

If you run the previous code, you will receive this output:

```
True
```

You call `all` with a generator as its argument. The generator produces a Boolean for each element in the `animals` list based on whether or not the animal starts with the letter `s`. The final return value is `True` because every element in the `animals` list starts with `s`.

> **Note:** You can often use [Generator expressions](https://www.python.org/dev/peps/pep-0289/) in place of list comprehensions as a way of saving memory. For example, consider `all(i < 8 for i in range(3))` and `all([i < 8 for i in range(3)])`. Both statements return `True` because 0, 1, 2 are all less than 8. The second example (which uses a list comprehension), however, has the added overhead of implicitly creating a list three entries long (`[True, True, True]`) and then passing that list to the `all` function. The first example (which uses generator expressions), by contrast, passes a generator object to the `all` function, which the `all` function iterates over directly without the overhead of an intermediary list.

Consider that the equivalent code written using a full-fledged `for` loop would have been significantly more verbose:

```python
animals = ["shark", "seal", "sea urchin"]

def all_animals_start_with_s(animals):
    for a in animals:
        if not a.startswith("s"):
            return False
    return True

print(all_animals_start_with_s(animals))
```

Without `all`, your code for determining if all the animals start with the letter `s` requires several more lines to implement.

Next, you'll look at the sibling function to `all`: `any`.

## Using `any`

You can use the built-in function `any` to check if any element in an iterable is `True`. For example:

```python
any([False, True])
```

If you run the previous code, you'll receive this output:

```
True
```

You call `any` and give it a list with two elements (a `False` Boolean value and a `True` Boolean value). Since one or more element in the iterable was `True`, the output was also `True`.

`any` will return `False` if, and only if, 0 of the elements in the given iterable are `True`:

```python
all([False, False, False])
```

If you run the previous code, you'll receive this output:

```
False
```

You call `any` with a list containing three elements (all `False` Boolean values). Since 0 of the elements in the iterable is `True`, the output of the call to `any` is `False`.

Notably, `any` stops iterating and immediately returns `True` as soon as it encounters the first `True` entry in an iterable. So, `any` can be useful if you want to check successive conditions, but return immediately as soon as one condition is `True`.

`any`—like its sibling method `all`—is particularly helpful when combined with generators and custom conditions (in place of a full `for` loop). Let's consider an example to find out whether there are elements in a list that end with `"urchin"`:

```python
animals = ["shark", "seal", "sea urchin"]
any(s.endswith("urchin") for s in animals)
```

You will receive this output:

```
True
```

You call `any` with a generator as its argument. The generator produces a Boolean for each element in the `animals` list based on whether or not the animal ends with `urchin`. The final return value is `True` because one element in the `animals` list ends with `urchin`.

> **Note:** When `any` is given an empty iterable (for example, an empty list like `any([])`), its return value is `False`. So, `any` returns `False` if there are 0 elements in the iterable or all the elements in the iterable are also `False`.

Next, you'll review another built-in function: `max`.

## Using `max`

The built-in function `max` returns the largest element given in its arguments. For example:

```python
max([0, 8, 3, 1])
```
`max` is given a list with four integers as its argument. The return value of `max` is the largest element in that list: `8`.

The output will be the following:

```
8
```

If given two or more positional arguments (as opposed to a single positional argument with an iterable), `max` returns the largest of the given arguments:

```python
max(1, -1, 3)
```

If you run the previous code, you will receive this output:

```
3
```

`max` is given three individual arguments, the largest of which being `3`. So, the return value of the call to `max` is `3`.

Just like `any` and `all`, `max` is particularly helpful because it requires fewer lines to use than equivalent code written as a full `for` loop.

`max` can also deal with objects more complex than numbers. For example, you can use `max` with dictionaries or other custom objects in your program. `max` can accommodate these objects by using its keyword argument named `key`.

You can use the `key` keyword argument to define a custom function that determines the value used in the comparisons to determine the maximum value. For example:

```python
entries = [{"id": 9}, {"id": 17}, {"id": 4}]
max(entries, key=lambda x: x["id"])
```

The output will be the following:

```
{'id': 17}
```

You call `max` with a list of dictionaries as its input. You give an anonymous `lambda` function as the `key` keyword argument. `max` calls the `lambda` function for every element in the `entries` list and returns the value of the `"id"` key of the given element. The final return value is the second element in `entries`: `{"id": 17}`. The second element in `entries` had the largest `"id"` value, and so was deemed to be the maximum element.

Note that when `max` is called with an empty iterable, it refuses to operate and instead raises a `ValueError`:

```python
max([])
```

If you run this code, you will receive the following output:

```
Traceback (most recent call last):
  File "max.py", line 1, in <module>
    max([])
ValueError: max() arg is an empty sequence
```

You call `max` with an empty list as its argument. `max` does not accept this as a valid input, and raises a `ValueError` Exception instead.

`max` has a counterpart called `min`, which you'll review next.

## Using `min`

The built-in function `min` returns the smallest element given in its arguments. For example:

```python
min([8, 0, 3, 1])
```
You give `min` a list with four integers as its argument. The return value of `min` is the smallest element in that list: `0`.

The output will be:

```
0
```

If given two or more positional arguments (as opposed to a single positional argument with an iterable), `min` returns the smallest of the given arguments:

```python
min(1, -1, 3)
```
If you run the previous code, you will receive this output:

```
-1
```

You give `min` three individual arguments, the smallest of which being `-1`. So, the return value of the call to `min` is `-1`.

Like `max`, `min` supports the keyword argument named `key` so that you can pass objects more complex than numbers into it. Using the `key` argument allows you to use the `min` function with any custom objects your program might define.

You can use the `key` keyword argument to define a custom function that determines the value used in the comparisons to determine the minimum value. For example:

```python
entries = [{"id": 9}, {"id": 17}, {"id": 4}]
min(entries, key=lambda x: x["id"])
```

```
{'id': 4}
```

You call `min` with a list of dictionaries as its input. You give an anonymous `lambda` function as the `key` keyword argument. `min` calls the `lambda` function for every element in the `entries` list and returns the value of the `"id"` key of the given element. The final return value is the third element in `entries`: `{"id": 4}`. The third element in `entries` had the smalled `"id"` value, and so was deemed to be the minimum element.

Like `max`, when you call `min` with an empty iterable, it refuses to operate and instead raises a `ValueError`:

```python
min([])
```

If you run the previous code, you will receive this output:

```
Traceback (most recent call last):
  File "min.py", line 1, in <module>
    min([])
ValueError: min() arg is an empty sequence
```

You call `min` with an empty list as its argument. `min` does not accept this as a valid input, and raises a `ValueError` Exception instead.

## Conclusion

In this tutorial, you used the Python built-in functions `all`, `any`, `max`, and `min`. You can learn more about the functions `all`, `any`, `max`, and `min` and other Python built-ins [in the Python docs](https://docs.python.org/3/library/functions.html).

*Editor: Kathryn Hancox*
