---
layout: post
title: How To Use argparse to Write Command-Line Programs in Python
date:  2021-03-10 14:00:00 -0700
categories: posts
---

# {{ page.title }}

<small style="font-weight: 175;">{{ page.date | date: "%-d %B, %Y"}}</small>

*This article was [originally published in DigitalOcean’s public knowledge base](https://www.digitalocean.com/community/tutorials/how-to-use-argparse-to-write-command-line-programs-in-python). It has been reproduced here with some minor edits.*

### Introduction

Python's [`argparse` standard library module](https://docs.python.org/3/library/argparse.html#module-argparsel) is a tool that helps you write command-line interfaces (CLI) over your Python code. You may already be familiar with CLIs: programs like `git`, `ls`, `grep`, and `find` all expose command-line interfaces that allow you to call an underlying program with specific inputs and options. `argparse` allows you to call your own custom Python code with command-line arguments similar to how you might invoke `git`, `ls`, `grep`, or `find` using the command line. You might find this useful if you want to allow other developers to run your code from the command line.

In this tutorial, you’ll use some of the utilities exposed by Python's `argparse` standard library module. You'll write command-line interfaces that accept positional and optional arguments to control the underlying program's behavior. You'll also self-document a CLI by providing help text that can be displayed to other developers who are using your CLI.

For this tutorial, you'll write command-line interfaces for a program that keeps track of fish in a fictional aquarium.

## Writing a Command-Line Program that Accepts a Positional Argument

You can use the `argparse` module to write a command-line interface that accepts a positional argument. Positional arguments (as opposed to optional arguments, which we'll explore in a subsequent section), are generally used to specify required inputs to your program.

Let's consider an example CLI that prints the fish in an aquarium tank identified by a positional `tank` argument.

To create the CLI, create a file named `aquarium.py` on your computer with the following contents:

```python
# aquarium.py
import argparse

tank_to_fish = {
    "tank_a": "shark, tuna, herring",
    "tank_b": "cod, flounder",
}

parser = argparse.ArgumentParser(description="List fish in aquarium.")
parser.add_argument("tank", type=str)
args = parser.parse_args()

fish = tank_to_fish.get(args.tank, "")
print(fish)
```

You can print out the fish in `tank_a` by running:


```shell
python3 aquarium.py tank_a
```

After running that command, you will receive output like the following:

```
shark, tuna, herring
```

Similarly, if you ran `aquarium.py` to print out the fish in `tank_b` with:


```shell
python3 aquarium.py tank_b
```

You would receive output like the following:

```
cod, flounder
```

Let's break down the code in `aquarium.py`.

First, you import the `argparse` module to make it available for use in your program. Next, you create a dictionary data structure `tank_to_fish` that maps tank names (like `tank_a` and `tank_b`) to string descriptions of fish held in those tanks.

You instantiate an instance of the `ArgumentParser` class and bind it to the `parser` variable. You can think of `parser` as the main point of entry for configuring your command-line interface. The `description` string provided to `parser` is—as you'll learn later—used in the automatically generated help text for the CLI exposed by `aquarium.py`.

Calling `add_argument` on parser allows you to add arguments accepted by your command-line interface. In this case, you add a single argument named `tank` that is a string type. Calling `parser.parse_args()` instructs `parser` to process and validate the command-line input passed to `aquarium.py` (for example, something like `tank_a`). Accessing the `args` returned by `parser.parse_args()` allows you to retrieve the value of the passed in `tank` argument, and use it to `print` out the fish in that tank.

At this point, you've written a command-line interface and executed your program to print fish. Now you need to describe how your CLI works to other developers. `argparse` has strong support for help text to document your CLIs. You'll learn more about help text next.

## Viewing Help Text

The `aquarium.py` file you just wrote in the previous section actually does more than print the fish in a specific tank. Since you're using `argparse`, the command-line interface exposed by `aquarium.py` will automatically include help and usage messages that a user can consult to learn more about your program.

Consider, for example, the usage message `aquarium.py` prints if you provide invalid arguments on the command line. Try invoking `aquarium.py` with the wrong arguments on the command line by running:

```shell
python3 aquarium.py --not-a-valid-argument
```

If you run this command, you'll receive output like this:

```
usage: aquarium.py [-h] tank
aquarium.py: error: the following arguments are required: tank
```

The output printed on the command line indicates that there was an error trying to run `aquarium.py`. The output indicates that the user needs to invoke `aquarium.py` with a `tank` argument. Something else you might notice is the `-h` in-between `[]` characters. This denotes that `-h` is an optional argument that you can provide as well.

Now you'll find out what happens when you call `aquarium.py` with the `-h` option. Try invoking `aquarium.py` with the `-h` argument on the command line by running:

```shell
python3 aquarium.py -h
```

If you run this command, you'll receive output like this:

```
usage: aquarium.py [-h] tank

List fish in aquarium.

positional arguments:
  tank

optional arguments:
  -h, --help  show this help message and exit
```

As you may have guessed, the `-h` option is short for, "help." Running `python3 aquarium.py -h` (or, equivalently, the longer variant `python3 aquarium.py --help`) prints out the help text. The help text, effectively, is a longer version of the usage text that was outputted in the previous example when you supplied invalid arguments. Notably, the help text also includes the custom `description` string of `List fish in an aquarium` that you instantiated the `ArgumentParser` with earlier on in this tutorial.

By default, when you write a CLI using `argparse` you'll automatically get help and usage text that you can use to document your CLI for other developers.

So far, you've written a CLI that accepts a required positional argument. In the next section you'll add an optional argument to your interface to expand on its capabilities.

## Adding an Optional Argument

Sometimes, it's helpful to include optional arguments in your command-line interface. These options are typically prefixed with two dash characters, for example `--some-option`. Let's rewrite `aquarium.py` with the following adjusted content that adds an `--upper-case` option to your CLI:

```python
# aquarium.py
import argparse

tank_to_fish = {
    "tank_a": "shark, tuna, herring",
    "tank_b": "cod, flounder",
}

parser = argparse.ArgumentParser(description="List fish in aquarium.")
parser.add_argument("tank", type=str)
parser.add_argument("--upper-case", default=False, action="store_true")
args = parser.parse_args()

fish = tank_to_fish.get(args.tank, "")

if args.upper_case:
    fish = fish.upper()

print(fish)
```

Try invoking `aquarium.py` with the new `--upper-case` argument by running the following:

```shell
python3 aquarium.py --upper-case tank_a
```

If you run this command, you'll receive output like this:

```
SHARK, TUNA, HERRING
```

The fish in `tank_a` are now outputted in upper case. You accomplished this by adding a new `--upper-case` option when you called `parser.add_argument("--upper-case", default=False, action="store_true")`. The `"--upper-case"` string is the name of the argument you'd like to add.

If the `--upper-case` option isn't provided by the user of the CLI, `default=False` ensures that its value is set to `False` by default. `action="store_true"` controls what happens when the `--upper-case` option is provided by the CLI user. There are a number of [different possible strings supported by the `action` parameter](https://docs.python.org/3/library/argparse.html#action), but `"store_true"` stores the value `True` into the argument, if it is provided on the command line.

Note that, although the argument is two words separated by a dash (`upper-case`), `argparse` makes it available to your code as `args.upper_case` (with an underscore separator) after you call `parser.parse_args()`. In general, `argparse` [converts any dashes in the provided arguments into underscores](https://docs.python.org/dev/library/argparse.html#dest) so that you have valid Python identifiers to reference after you call `parse_args()`.

As before, `argparse` automatically creates a `--help` option and documents your command-line interface (including the `--upper-case` option you just added).

Try invoking `aquarium.py` with the `--help` option again to receive the updated help text:

```shell
python3 aquarium.py --help
```

Your output will be similar to:

```
usage: aquarium.py [-h] [--upper-case] tank

List fish in aquarium.

positional arguments:
  tank

optional arguments:
  -h, --help    show this help message and exit
  --upper-case
```

`argparse` automatically documented the positional `tank` argument, the optional `--upper-case` option, and the built-in `--help` option as well.

This help text is useful, but you can improve it with additional information to help users better understand how they can invoke your program. You'll explore how to enhance the help text in the next section.

## Exposing Additional Help Text to Your Users

Developers use the help text provided by your command-line interfaces to understand what your program is capable of and how they should use it. Let's revise `aquarium.py` again so it includes better help text. You can specify argument-level information by specifying `help` strings in the `add_argument` calls. Rewrite `aquarium.py` again so it contains the following contents:

```python
# aquarium.py
import argparse

tank_to_fish = {
    "tank_a": "shark, tuna, herring",
    "tank_b": "cod, flounder",
}

parser = argparse.ArgumentParser(description="List fish in aquarium.")
parser.add_argument("tank", type=str, help="Tank to print fish from.")
parser.add_argument(
    "--upper-case",
    default=False,
    action="store_true",
    help="Upper case the outputted fish.",
)
args = parser.parse_args()

fish = tank_to_fish[args.tank]

if args.upper_case:
    fish = fish.upper()

print(fish)
```

Try invoking `aquarium.py` with the `--help` option again to receive the updated help text:

```shell
python3 aquarium.py --help
```

Your output will be the following:

```
usage: aquarium.py [-h] [--upper-case] tank

List fish in aquarium.

positional arguments:
  tank          Tank to print fish from.

optional arguments:
  -h, --help    show this help message and exit
  --upper-case  Upper case the outputted fish.
```

In this latest output, notice that the `tank` positional argument and the `--upper-case` optional argument both include custom help text. You provided this extra help text by supplying strings to the `help` part of `add_argument`. (For example, `parser.add_argument("tank", type=str, help="Tank to print fish from.")`.) `argparse` takes these strings and renders them for you in the help text output.

You can improve your help text further by having `argparse` print out any default values you have defined.

## Displaying Default Values in Help Text

If you use a custom `formatter_class` when you instantiate your `ArgumentParser` instance, `argparse` will include default values in the help text output. Try adding `argparse.ArgumentDefaultsHelpFormatter` as your `ArgumentParser` formatter class:

```python
# aquarium.py
import argparse

tank_to_fish = {
    "tank_a": "shark, tuna, herring",
    "tank_b": "cod, flounder",
}

parser = argparse.ArgumentParser(
    description="List fish in aquarium.",
    formatter_class=argparse.ArgumentDefaultsHelpFormatter,
)
parser.add_argument("tank", type=str, help="Tank to print fish from.")
parser.add_argument(
    "--upper-case",
    default=False,
    action="store_true",
    help="Upper case the outputted fish.",
)
args = parser.parse_args()

fish = tank_to_fish[args.tank]

if args.upper_case:
    fish = fish.upper()

print(fish)
```

Now, try invoking `aquarium.py` with the `--help` option again to check the updated help text:

```shell
python3 aquarium.py --help
```

After running this command, you'll receive output like this:

```
usage: aquarium.py [-h] [--upper-case] tank

List fish in aquarium.

positional arguments:
  tank          Tank to print fish from.

optional arguments:
  -h, --help    show this help message and exit
  --upper-case  Upper case the outputted fish. (default: False)
```

In this latest output, notice that the documentation for `--upper-case` ends with an indication of the default value for the `--upper-case` option (`default: False`). By including `argparse.ArgumentDefaultsHelpFormatter` as the [`formatter_class`](https://docs.python.org/3/library/argparse.html#formatter-class) of your `ArgumentParser`, `argparse` automatically started rendering default value information in its help text.

## Conclusion

The `argparse` module is a powerful part of the Python standard library that allows you to write command-line interfaces for your code. This tutorial introduced you to the foundations of `argparse`: you wrote a command-line interface that accepted positional and optional arguments, and exposed help text to the user.

`argparse` supports many more feautures that you can use to write command-line programs with sophisticated sets of inputs and validations. From here, you can use [the `argparse` module's documentation](https://docs.python.org/3/library/argparse.html#module-argparse) to learn more about other available classes and utilities.

*Editor: Kathryn Hancox*
