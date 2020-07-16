---
layout: post
title: How To Use the pathlib Module to Manipulate Filesystem Paths in Python 3
date:  2020-07-20 17:00:00 -0700
categories: posts
---

# {{ page.title }}

<small style="font-weight: 175;">{{ page.date | date: "%-d %B, %Y"}}</small>

*This article was [originally published in DigitalOcean’s public knowledge base](https://www.digitalocean.com/community/tutorials/how-to-use-the-pathlib-module-to-manipulate-filesystem-paths-in-python-3). It has been reproduced here with some minor edits.*

# How To Use the pathlib Module to Manipulate Filesystem Paths in Python 3

### Introduction

Python 3 includes [the `pathlib` module](https://docs.python.org/3/library/pathlib.html) for manipulating filesystem paths agnostically whatever the operating system. `pathlib` is similar to the [`os.path` module](https://docs.python.org/3.7/library/os.path.html), but `pathlib` offers a higher level—and often times more convenient—interface than `os.path`.

We can identify files on a computer with hierarchical paths. For example, we might identify the file `wave.txt` on a computer with this path: `/Users/sammy/ocean/wave.txt`. Operating systems represent paths slightly differently. Windows might represent the path to the `wave.txt` file like `C:\Users\sammy\ocean\wave.txt`.

You might find the `pathlib` module useful if in your Python program you are creating or moving files on the filesystem, listing files on the filesystem that all match a given extension or pattern, or creating operating system appropriate file paths based on collections of raw strings. While you might be able to use other tools (like the `os.path` module) to accomplish many of these tasks, the `pathlib` module allows you to perform these operations with a high degree of readability and minimal amount of code.

In this tutorial, we'll go over some of the ways to use the `pathlib` module to represent and manipulate filesystem paths.

## Prerequisites

To get the most out of this tutorial, it is recommended to have some familiarity with programming in Python 3. You can review these tutorials for the necessary background information:

- [How To Code in Python 3](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-python-3)

## Constructing `Path` Instances

The `pathlib` module provides several classes, but one of the most important is the `Path` class. Instances of the `Path` class represent a path to a file or directory on our computer's filesystem.

For example, the following code instantiates a `Path` instance that represents part of the path to a `wave.txt` file:

```python
from pathlib import Path

wave = Path("ocean", "wave.txt")
print(wave)
```

If we run this code, we'll receive output like the following:

```
ocean/wave.txt
```

`from pathlib import Path` makes the `Path` class available to our program. Then `Path("ocean", "wave.txt")` instantiates a new `Path` instance. Printing the output shows that Python has added the appropriate operating system separator of `/` between the two path components we gave it: `"ocean"` and `"wave.txt"`.

> **Note:** Depending on your operating system, your output may vary slightly from the example outputs shown in this tutorial. If you are running Windows, for example, your output for this first example might look like `ocean\wave.txt`.

Currently, the `Path` object assigned to the `wave` variable contains a [_relative path_](https://en.wikipedia.org/wiki/Path_%28computing%29#Absolute_and_relative_paths). In other words, `ocean/wave.txt` might exist in several places on our filesystem. As an example, it may exist in `/Users/user_1/ocean/wave.txt` or `/Users/user_2/research/ocean/wave.txt`, but we haven't specified exactly which one we are referring to. An [_absolute path_](https://en.wikipedia.org/wiki/Path_%28computing%29#Absolute_and_relative_paths), by contrast, unambiguously refers to one location on the filesystem.

You can use `Path.home()` to get the absolute path to the home directory of the current user:

```python
home = Path.home()
wave_absolute = Path(home, "ocean", "wave.txt")
print(home)
print(wave_absolute)
```

If we run this code, we'll receive output roughly like the following:

```
/Users/sammy
/Users/sammy/ocean/wave.txt
```

> **Note:** As mentioned earlier, your output will vary depending on your operating system. Your home directory, of course, will also be different than `/Users/sammy`.

`Path.home()` returns a `Path` instance with an absolute path to the current user's home directory. We then pass in this `Path` instance and the strings `"ocean"` and `"wave.txt"` into another `Path` constructor to create an absolute path to the `wave.txt` file. The output shows the first line is the home directory, and the second line is the home directory plus `ocean/wave.txt`.

This example also illustrates an important feature of the `Path` class: the `Path` constructor accepts both strings and preexisting `Path` objects.

Let's look at the support of both strings and `Path` objects in the `Path` constructor a little more closely:

```python
shark = Path(Path.home(), "ocean", "animals", Path("fish", "shark.txt"))
print(shark)
```

If we run this Python code, we'll receive output similar to the following:

```
/Users/sammy/ocean/animals/fish/shark.txt
```

`shark` is a `Path` to a file that we constructed using both `Path` objects (`Path.home()` and `Path("fish", "shark.txt")`) and strings (`"ocean"` and `"animals"`). The `Path` constructor intelligently handles both types of objects and cleanly joins them using the appropriate operating system separator, in this case `/`.

## Accessing File Attributes

Now that we've learned how to construct `Path` instances, let's review how you can use those instances to access information about a file.

We can use the `name` and `suffix` attributes to access file names and file suffixes:

```python
wave = Path("ocean", "wave.txt")
print(wave)
print(wave.name)
print(wave.suffix)
```

Running this code, we'll receive output similar to the following:

```
/Users/sammy/ocean/wave.txt
wave.txt
.txt
```

This output shows that the name of the file at the end of our path is `wave.txt` and the suffix of that file is `.txt`.

`Path` instances also offer the `with_name` function that allow you to seamlessly create a new `Path` object with a different name:

```python
wave = Path("ocean", "wave.txt")
tides = wave.with_name("tides.txt")
print(wave)
print(tides)
```

If we run this, we'll receive output like the following:

```python
ocean/wave.txt
ocean/tides.txt
```

The code first constructs a `Path` instance that points to a file named `wave.txt`. Then, we call the `with_name` method on `wave` to return a second `Path` instance that points to a new file named `tides.txt`. The `ocean/` directory portion of the path remains unchanged, leaving the final path as `ocean/tides.txt`

## Accessing Ancestors

Sometimes it is useful to access directories that contain a given path. Let's consider an example:

```python
shark = Path("ocean", "animals", "fish", "shark.txt")
print(shark)
print(shark.parent)
```

If we run this code, we'll receive output that looks like the following:

```
ocean/animals/fish/shark.txt
ocean/animals/fish
```

The `parent` attribute on a `Path` instance returns the most immediate ancestor of a given file path. In this case, it returns the directory that contains the `shark.txt` file: `ocean/animals/fish`.

We can access the `parent` attribute multiple times in a row to traverse up the ancestry tree of a given file:

```python
shark = Path("ocean", "animals", "fish", "shark.txt")
print(shark)
print(shark.parent.parent)
```

If we run this code, we'll receive the following output:

```
ocean/animals/fish/shark.txt
ocean/animals
```

The output is similar to the earlier output, but now we've traversed yet another level higher by accessing `.parent` a second time. Two directories up from `shark.txt` is the `ocean/animals` directory.

## Using Glob to List Files

It's also possible to use the `Path` class to list files using the `glob` method.

Let's say we had a directory structure that looked like this:

```
└── ocean
    ├── animals
    │   └── fish
    │       └── shark.txt
    ├── tides.txt
    └── wave.txt
```

An `ocean` directory contains the files `tides.txt` and `wave.txt`. We have a file named `shark.txt` nested under the `ocean` directory, an `animals` directory, and a `fish` directory: `ocean/animals/fish`.

To list all the `.txt` files in the `ocean` directory, we could say:

```python
for txt_path in Path("ocean").glob("*.txt"):
    print(txt_path)
```

This code would yield output like:

```
ocean/wave.txt
ocean/tides.txt
```

The `"*.txt"` glob pattern finds all files ending in `.txt`. Since the code sample executes that glob in the `ocean` directory, it returns the two `.txt` files in the `ocean` directory: `wave.txt` and `tides.txt`.


> **Note:** If you would like to duplicate the outputs shown in this example, you'll need to mimic the directory structure illustrated here on your computer.

We can also use the `glob` method recursively. To list all the `.txt` files in the `ocean` directory and all its subdirectories, we could say:

```python
for txt_path in Path("ocean").glob("**/*.txt"):
    print(txt_path)
```

If we run this code, we'd receive output like the following:

```
ocean/wave.txt
ocean/tides.txt
ocean/animals/fish/shark.txt
```

The `**` part of the glob pattern will match this directory and all directories beneath it, recursively. So, not only do we have the `wave.txt` and `tides.txt` files in the output, but we also receive the `shark.txt` file that was nested under `ocean/animals/fish`.

## Computing Relative Paths

We can use the `Path.relative_to` method to compute paths relative to one another. The `relative_to` method is useful when, for example, you want to retrieve a portion of a long file path.

Consider the following code:

```python
shark = Path("ocean", "animals", "fish", "shark.txt")
below_ocean = shark.relative_to(Path("ocean"))
below_animals = shark.relative_to(Path("ocean", "animals"))
print(shark)
print(below_ocean)
print(below_animals)
```

If we run this, we'll receive output like the following:

```
ocean/animals/fish/shark.txt
animals/fish/shark.txt
fish/shark.txt
```

The `relative_to` method returns a new `Path` object relative to the given argument. In our example, we compute the `Path` to `shark.txt` relative to the `ocean` directory, and then relative to both the `ocean` and `animals` directories.

If `relative_to` can't compute an answer because we give it an unrelated path, it raises a `ValueError`:

```python
shark = Path("ocean", "animals", "fish", "shark.txt")
shark.relative_to(Path("unrelated", "path"))
```

We'll receive a `ValueError` exception raised from this code that will be something like this:

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/lib/Python3.8/pathlib.py", line 899, in relative_to
    raise ValueError("{!r} does not start with {!r}"
ValueError: 'ocean/animals/fish/shark.txt' does not start with 'unrelated/path'
```

`unrelated/path` is not a part of `ocean/animals/fish/shark.txt`, so there's no way for Python to compute a relative path for us.

## Conclusion

The `pathlib` module is a powerful part of the [Python Standard Library](https://docs.python.org/3/library/) that lets us manipulate filesystem paths quickly on any operating system. In this tutorial, we have learned to use some of `pathlib`'s key utilities for accessing file attributes, listing files with glob patterns, and traversing parent files and directories.

The `pathlib` module exposes additional classes and utilities that we did not cover in this tutorial. Now that you have a baseline, you can use [the `pathlib` module's documentation](https://docs.python.org/3/library/pathlib.html) to learn more about other available classes and utilities.

*Editor: Kathryn Hancox*
