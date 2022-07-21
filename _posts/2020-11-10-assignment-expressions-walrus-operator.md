---
layout: post
title: How To Use Assignment Expressions in Python
date:  2020-11-10 14:00:00 -0700
categories: posts
---

# {{ page.title }}

<small style="font-weight: 175;">{{ page.date | date: "%-d %B, %Y"}}</small>

*This article was [originally published in DigitalOcean’s public knowledge base](https://www.digitalocean.com/community/tutorials/how-to-use-assignment-expressions-in-python). It has been reproduced here with some minor edits.*

### Introduction

[Python 3.8](https://www.python.org/downloads/release/python-380/), released in October 2019, adds assignment expressions to Python via the `:=` syntax. The assignment expression syntax is also sometimes called "the walrus operator" because `:=` vaguely resembles a walrus with tusks.

Assignment expressions allow variable assignments to occur inside of larger expressions. While assignment expressions are never strictly necessary to write correct Python code, they can help make existing Python code more concise. For example, assignment expressions using the `:=` syntax allow variables to be assigned inside of `if` statements, which can often produce shorter and more compact sections of Python code by eliminating variable assignments in lines preceding or following the `if` statement.

In this tutorial, you will use assignment expressions in several examples to produce concise sections of code.

## Prerequisites

To get the most out of this tutorial, you will need:

* Python 3.8 or above. Assignment expressions are a new feature added starting in Python 3.8.

## Using Assignment Expressions in `if` Statements

Let's start with an example of how you can use assignment expressions in an `if` statement.

Consider the following code that checks the length of a list and prints a statement:

```python
some_list = [1, 2, 3]
if (list_length := len(some_list)) > 2:
    print("List length of", list_length, "is too long")
```

If you run the previous code, you will receive the following output:

```
List length of 3 is too long
```

You initialize a list named `some_list` that contains three elements. Then, the `if` statement uses the assignment expression `((list_length := len(some_list))` to bind the variable named `list_length` to the length of `some_list`. The `if` statement evaluates to `True` because `list_length` is greater than `2`. You print a string using the `list_length` variable, which you bound initially with the assignment expression, indicating the the three-element list is too long.

> **Note:** Assignment expressions are a new feature introduced in [Python 3.8](https://docs.python.org/3/whatsnew/3.8.html). To run the examples in this tutorial, you will need to use Python 3.8 or higher.

Had we not used assignment expression, our code might have been slightly longer. For example:

```python
some_list = [1, 2, 3]
list_length = len(some_list)
if list_length > 2:
    print("List length of", list_length, "is too long")
```

This code sample is equivalent to the first example, but this code requires one extra standalone line to bind the value of `list_length` to `len(some_list)`.

Another equivalent code sample might just compute `len(some_list)` twice: once in the `if` statement and once in the `print` statement. This would avoid incurring the extra line required to bind a variable to the value of `len(some_list)`:

```python
some_list = [1, 2, 3]
if len(some_list) > 2:
    print("List length of", len(some_list), "is too long")
```

Assignment expressions help avoid the extra line or the double calculation.

> **Note:** Assignment expressions are a helpful tool, but are not strictly necessary. Use your judgement and add assignment expressions to your code when it significantly improves the readability of a passage.

In the next section, we'll explore using assignment expressions inside of `while` loops.

## Using Assignment Expressions in `while` Loops

Assignment expressions often work well in `while` loops because they allow us to fold more context into the loop condition.

Consider the following example that embeds a user `input` function inside the `while` loop condition:

```python
while (directive := input("Enter text: ")) != "stop":
    print("Received directive", directive)
```

If you run this code, Python will continually prompt you for text input from your keyboard until you type the word `stop`. One example session might look like:

```python
>>> while (directive := input("Enter text: ")) != "stop":
...     print("Received directive", directive)
...
Enter text: hello
Received directive hello
Enter text: example
Received directive example
Enter text: stop
>>>
```

The assignment expression `(directive := input("Enter text: "))` binds the value of `directive` to the value retrieved from the user via the `input` function. You bind the return value to the variable `directive`, which you print out in the body of the `while` loop. The `while` loop exits whenever the you type `stop`.

Had you not used an assignment expression, you might have written an equivalent `input` loop like:

```python
directive = input("Enter text: ")
while directive != "stop":
    print("Received directive", directive)
    directive = input("Enter text: ")
```

This code is functionally identical to the one with assignment expressions, but requires four total lines (as opposed to two lines). It also duplicates the `input("Enter text: ")` call in two places. Certainly, there are many ways to write an equivalent `while` loop, but the assignment expression variant introduced earlier is compact and captures the program's intention well.

So far, you've used assignment expression in `if` statements and `while` loops. In the next section, you'll use an assignment expression inside of a list comprehension.

## Using Assignment Expressions in List Comprehensions

We can also use assignment expressions in list comprehensions. List comprehensions allow you to build lists succinctly by iterating over a sequence and potentially adding elements to the list that satisfy some condition.

Consider the following example that uses a list comprehension and an assignment expression to build a list of multiplied integers:

```python
def slow_calculation(x):
    print("Multiplying", x, "*", x)
    return x * x

[result for i in range(3) if (result := slow_calculation(i)) > 0]
```

If you run the previous code, you will receive the following:

```
Multiplying 0 * 0
Multiplying 1 * 1
Multiplying 2 * 2
[1, 4]
```

You define a function named `slow_calculation` that multiplies the given number `x` with itself. A list comprehension then iterates through `0`, `1`, and `2` returned by `range(3)`. An assignment expression binds the value `result` to the return of `slow_calculation` with `i`. You add the `result` to the newly built list as long as it is greater than 0. In this example, `0`, `1`, and `2` are all multiplied with themselves, but only the results `1` (`1 * 1`) and `4` (`2 * 2`) satisfy the greater than `0` condition and become part of the final list `[1, 4]`.

The `slow_calculation` function isn't necessarily slow in absolute terms, but is meant to illustrate an important point about effeciency. Consider an alternate implementation of the previous example without assignment expressions:


```python
def slow_calculation(x):
    print("Multiplying", x, "*", x)
    return x * x

[slow_calculation(i) for i in range(3) if slow_calculation(i) > 0]
```

Running this, you will receive the following output:

```
Multiplying 0 * 0
Multiplying 1 * 1
Multiplying 1 * 1
Multiplying 2 * 2
Multiplying 2 * 2
[1, 4]
```

In this variant of the previous code, you use no assignment expressions. Instead, you call `slow_calculation` up to two times: once to ensure `slow_calculation(i)` is greater than `0`, and potentially a second time to add the result of the calculation to the final list. `0` is only multiplied with itself once because `0 * 0` is not greater than `0`. The other results, however, are doubly calculated because they satisfy the greater than 0 condition, and then have their results recalculated to become part of the final list `[1, 4]`.

You've now combined assignment expressions with list comprehensions to create blocks of code that are both efficient and concise.

## Conclusion

In this tutorial, you used assignment expressions to make compact sections of Python code that assign values to variables inside of `if` statements, `while` loops, and list comprehensions.

For more information on other assignment expressions, you can view [PEP 572](https://www.python.org/dev/peps/pep-0572/)—the document that initially proposed adding assignment expressions to Python.

*Editor: Kathryn Hancox*
