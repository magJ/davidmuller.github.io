---
layout: post
title: How To Use f-strings to Create Strings in Python 3
date:  2020-09-10 17:00:00 -0700
categories: posts
---

# {{ page.title }}

<small style="font-weight: 175;">{{ page.date | date: "%-d %B, %Y"}}</small>

*This article was [originally published in DigitalOcean’s public knowledge base](https://www.digitalocean.com/community/tutorials/how-to-use-f-strings-to-create-strings-in-python-3). It has been reproduced here with some minor edits.*

### Introduction

Python strings are variable length sequences of characters and symbols. Strings allow your program to manipulate and track text and also display that text to your users.

Python includes several methods for building strings including the `%`-formatting style and the `str.format()` method. The `str.format()` method is newer than the `%`-formatting style. f-string formatting is the newest method of string formatting in Python and offers conveniences, such as using expressions within strings, which aren't available in either the `%`-formatting style or the `str.format()` method

In this tutorial, you will learn how to use Python 3's f-strings to create strings dynamically.

## Using Variables in f-strings

Programs commonly need to substitute a variable into a string. Python's f-strings provide a convenient way for us to do this. Let's consider an example:

```python
ocean_description = "deep blue"
print(f"The ocean is {ocean_description} today")
```

If we run this code, we'll receive output like the following:

```
The ocean is deep blue today
```

First, we bind the string `deep blue` to a variable named `ocean_description`. On the next line, we activate f-string formatting by prefixing a string with `f`. f-strings interpret the expressions inside of `{}` characters as Python. So, by saying `{ocean_description}`, we are instructing Python to insert the value of the `ocean_description` variable into our string. The resulting string is then printed: `The ocean is deep blue today`.

## Using Arbitrary Expressions in f-strings

In the previous section, we learned how to substitute a variable into an f-string. f-strings also allow the substitution of arbitrary Python expressions:

```python
print(f"1 + 1 = {1 + 1}")
```

If we run this code, we'll receive output like the following:

```
1 + 1 = 2
```

In this example, we again activate f-string formatting by including the `f` prefix on a string. Inside of the `{}` characters, we include a valid Python expression of `1 + 1`. The end result of that expression is `2`, which gets included in the final string that is printed.

Just about any valid Python expression can be used in an f-string. In the next example, we'll demonstrate accessing a dictionary value inside of an f-string:

```python
ocean_dict = {"shark": "fish"}
print(f"A shark is a kind of {ocean_dict['shark']}")
```


We'll receive output like the following:

```
A shark is a kind of fish
```

On the first line we define a dictionary with a single key/value pair: the key `shark` is mapped to the value `fish`. On the next line, we embed the expression `ocean_dict['shark']` inside of `{}` characters in an f-string. The result of that embedded expression is `fish`, which is included in the output.

## Including the `repr` of an Object

By default, f-strings will coerce included Python objects to strings so that the objects can be made a part of the final string. By default, Python uses the [`__str__` method](https://docs.python.org/3/reference/datamodel.html#object.__str__) defined on objects to coerce them to strings. Built-in Python objects (for example: lists, dictionaries, integers, floats) include predefined `__str__` methods so that the coercion process happens seamlessly.

Sometimes it's helpful to use an alternate representation of the object in the generated string. f-strings include a directive that allows the [`repr`](https://docs.python.org/3/library/functions.html#repr) of a Python object to be included in the final output. The `repr` of a Python object is a debugging description of the given Python object.

Let's consider an example to make the distinction between `__str__` and `repr` clearer. To call `repr` on the variable we add `!r` to the end of the expression:

```python
from datetime import datetime

now = datetime.now()
print(f"{now}")
print(f"{now!r}")
```

If we run this code, we receive output like the following:

```
2020-08-22 18:23:22.233108
datetime.datetime(2020, 8, 22, 18, 23, 22, 233108)
```


> **Note:** Generally, your output will contain text related to the current time on your computer. The time on your computer will be different than the time displayed in the sample output.

In this example, we use the [`datetime` module](https://docs.python.org/3/library/datetime.html) to get an object representing the current time and bind it to a variable named `now`. Next, we print out `now` twice using two different f-strings. In the first f-string, we don't include any additional modifiers and so Python uses the `__str__` representation of the current time.

In the second example, we add the `!r` modifier to the end of the expression embedded in the f-string. `!r` instructs Python to call [`repr`](https://docs.python.org/3/library/functions.html#repr) on `now` to generate a string representation. In general, `repr` prints debugging information more suitable for the interactive Python interpreter. In this case, we receive details about the `datetime` object that was bound to `now`.

## Using Format Specs

f-strings offer format spec modifiers for additional control of outputted strings. These format spec modifiers are prefixed by a `:`, for example:

```
{expression:format_spec}
```

The percentage modifier is an example of one available modifier:

```python
decimal_value = 18.12 / 100.0
print(f"{decimal_value:.1%}")
```
If we run this code, we'll receive output like the following:

```
18.1%
```

First, we bind `decimal_value` to `0.1812` (the result of `18.12 / 100.0`). Then, we use an f-string to print out `decimal_value` as a percentage. The `:` begins the format spec section. The `.1` indicates that we'd like to output the number with 1 decimal point of precision. The `%` is the type of format spec we'd like to use. The `%` format spec multiplies the number by 100 and includes a `%` symbol in the output. So, the final output is `18.12%` (`0.1812 * 100` rounded to 1 decimal place, and a `%` symbol).

Format specs allow you to do more than print out percentages; they are a complex miniature language on their own. They allow you to, for example, include padding characters around an expression or print numbers in scientific notation.

You can use [this section of the Python docs](https://docs.python.org/3/library/string.html#format-specification-mini-language) to learn more about Python's available format specs.

## Dealing with Special Characters

As we've seen, `{}` characters have special meaning in f-strings. If we want to print a literal `{` or `}` character, we'll need to escape them by adding additional `{` or `}` characters. Consider the following example:

```python
print(f"{{}}")
```

If we run this code, we'll receive output like:

```
{}
```

In order to print out a literal `{}`, we needed to double both the `{` and the `}` since these characters usually have special meaning.

## Conclusion

f-strings are a powerful and convenient way to build strings dynamically and concisely in Python. In this tutorial, you have learned how to substitute variables and expressions into f-strings, use type-conversion specifiers, and escape special characters with f-strings.

The `string` module exposes additional classes and utilities that we did not cover in this tutorial. Now that you have a baseline, you can use [the `string` module's documentation](https://docs.python.org/3/library/string.html) to learn more about other available classes and utilities.

*Editor: Kathryn Hancox*
