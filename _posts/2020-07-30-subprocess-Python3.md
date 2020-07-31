---
layout: post
title: How To Use subprocess to Run External Programs in Python 3
date:  2020-07-30 11:00:00 -0700
categories: posts
---

# {{ page.title }}

<small style="font-weight: 175;">{{ page.date | date: "%-d %B, %Y"}}</small>

*This article was [originally published in DigitalOcean’s public knowledge base](https://www.digitalocean.com/community/tutorials/how-to-use-subprocess-to-run-external-programs-in-python-3). It has been reproduced here with some minor edits.*

### Introduction

Python 3 includes the [`subprocess` module](https://docs.python.org/3/library/subprocess.html) for running external programs and reading their outputs in your Python code.

You might find `subprocess` useful if you want to use another program on your computer from within your Python code. For example, you might want to invoke [`git`](https://www.digitalocean.com/community/tutorials/how-to-contribute-to-open-source-getting-started-with-git) from within your Python code to retrieve files in your project that are tracked in `git` version control. Since any program you can access on your computer can be controlled by `subprocess`, the examples shown here will be applicable to any external program you might want to invoke from your Python code.

`subprocess` includes several classes and functions, but in this tutorial we'll cover one of `subprocess`'s most useful functions: [`subprocess.run`](https://docs.python.org/3.7/library/subprocess.html#subprocess.run). We'll review its different uses and main keyword arguments.

## Prerequisites

To get the most out of this tutorial, it is recommended to have some familiarity with programming in Python 3. You can review these tutorials for the necessary background information:

* [How To Code in Python 3](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-python-3)

## Running an External Program

You can use the `subprocess.run` function to run an external program from your Python code. First, though, you need to import the `subprocess` and `sys` modules into your program:

```python
import subprocess
import sys

result = subprocess.run([sys.executable, "-c", "print('ocean')"])
```

If you run this, you will receive output like the following:

```
ocean
```

Let's review this example:

- [`sys.executable`](https://docs.python.org/3/library/sys.html#sys.executable) is the absolute path to the Python executable that your program was originally invoked with. For example, `sys.executable` might be a path like `/usr/local/bin/python`.
- `subprocess.run` is given a list of strings consisting of the components of the command we are trying to run. Since the first string we pass is `sys.executable`, we are instructing `subprocess.run` to execute a new Python program.
- The `-c` component is a `python` command line option that allows you to pass a string with an entire Python program to execute. In our case, we pass a program that prints the string `ocean`.

You can think of each entry in the list that we pass to `subprocess.run` as being separated by a space. For example, `[sys.executable, "-c", "print('ocean')"]` translates roughly to `/usr/local/bin/python -c "print('ocean')"`. Note that `subprocess` automatically quotes the components of the command before trying to run them on the underlying operating system so that, for example, you can pass a filename that has spaces in it.

> **Warning:** Never pass untrusted input to `subprocess.run`. Since `subprocess.run` has the ability to perform arbitrary commands on your computer, malicious actors can use it to manipulate your computer in unexpected ways.

## Capturing Output From an External Program

Now that we can invoke an external program using `subprocess.run`, let's see how we can capture output from that program. For example, this process could be useful if we wanted to use `git ls-files` to output all your files currently stored under version control.

> **Note:** The examples shown in this section require Python 3.7 or higher. In particular, the `capture_output` and `text` keyword arguments were added in Python 3.7 when it was released in June 2018.

Let's add to our previous example:

```python
import subprocess
import sys

result = subprocess.run(
    [sys.executable, "-c", "print('ocean')"], capture_output=True, text=True
)
print("stdout:", result.stdout)
print("stderr:", result.stderr)
```

If we run this code, we'll receive output like the following:

```
stdout: ocean

stderr:
```

This example is largely the same as the one introduced in the first section: we are still running a subprocess to print `ocean`. Importantly, however, we pass the `capture_output=True` and `text=True` keyword arguments to `subprocess.run`.

`subprocess.run` returns a [`subprocess.CompletedProcess`](https://docs.python.org/3/library/subprocess.html#subprocess.CompletedProcess) object that is bound to `result`. The `subprocess.CompletedProcess` object includes details about the external program's exit code and its output. `capture_output=True` ensures that `result.stdout` and `result.stderr` are filled in with the corresponding output from the external program. By default, `result.stdout` and `result.stderr` are bound as bytes, but the `text=True` keyword argument instructs Python to instead decode the bytes into strings.

In the output section, `stdout` is `ocean` (plus the trailing newline that `print` adds implicitly), and we have no `stderr`.

Let's try an example that produces a non-empty value for `stderr`:

```python
import subprocess
import sys

result = subprocess.run(
    [sys.executable, "-c", "raise ValueError('oops')"], capture_output=True, text=True
)
print("stdout:", result.stdout)
print("stderr:", result.stderr)
```

If we run this code, we receive output like the following:

```
stdout:
stderr: Traceback (most recent call last):
  File "<string>", line 1, in <module>
ValueError: oops

```

This code runs a Python subprocess that immediately raises a `ValueError`. When we inspect the final `result`, we see nothing in `stdout` and a `Traceback` of our `ValueError` in `stderr`. This is because by default Python writes the `Traceback` of the unhandled exception to `stderr`.

## Raising an Exception on a Bad Exit Code

Sometimes it's useful to raise an exception if a program we run exits with a bad exit code. Programs that exit with a zero code are considered successful, but programs that exit with a non-zero code are considered to have encountered an error. As an example, this pattern could be useful if  we wanted to raise an exception in the event that we run `git ls-files` in a directory that wasn't actually a `git` repository.

We can use the `check=True` keyword argument to `subprocess.run` to have an exception raised if the external program returns a non-zero exit code:

```python
import subprocess
import sys

result = subprocess.run([sys.executable, "-c", "raise ValueError('oops')"], check=True)
```

If we run this code, we receive output like the following:

```
Traceback (most recent call last):
  File "<string>", line 1, in <module>
ValueError: oops
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/lib/python3.8/subprocess.py", line 512, in run
    raise CalledProcessError(retcode, process.args,
subprocess.CalledProcessError: Command '['/usr/local/bin/python', '-c', "raise ValueError('oops')"]' returned non-zero exit status 1.
```

This output shows that we ran a subprocess that raised an error, which is printed in `stderr` in our terminal. Then `subprocess.run` dutifully raised a `subprocess.CalledProcessError` on our behalf in our main Python program.

Alternatively, the `subprocess` module also includes the [`subprocess.CompletedProcess.check_returncode`](https://docs.python.org/3/library/subprocess.html#subprocess.CompletedProcess.check_returncode) method, which we can invoke for similar effect:

```python
import subprocess
import sys

result = subprocess.run([sys.executable, "-c", "raise ValueError('oops')"])
result.check_returncode()
```

If we run this code, we'll receive:

```
Traceback (most recent call last):
  File "<string>", line 1, in <module>
ValueError: oops
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/lib/python3.8/subprocess.py", line 444, in check_returncode
    raise CalledProcessError(self.returncode, self.args, self.stdout,
subprocess.CalledProcessError: Command '['/usr/local/bin/python', '-c', "raise ValueError('oops')"]' returned non-zero exit status 1.
```

Since we didn't pass `check=True` to `subprocess.run`, we successfully bound a `subprocess.CompletedProcess` instance to `result` even though our program exited with a non-zero code. Calling `result.check_returncode()`, however, raises a `subprocess.CalledProcessError` because it detects the completed process exited with a bad code.

## Using timeout to Exit Programs Early

`subprocess.run` includes the `timeout` argument to allow you to stop an external program if it is taking too long to execute:

```python
import subprocess
import sys

result = subprocess.run([sys.executable, "-c", "import time; time.sleep(2)"], timeout=1)
```

If we run this code, we'll receive output like the following:

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/lib/python3.8/subprocess.py", line 491, in run
    stdout, stderr = process.communicate(input, timeout=timeout)
  File "/usr/local/lib/python3.8/subprocess.py", line 1024, in communicate
    stdout, stderr = self._communicate(input, endtime, timeout)
  File "/usr/local/lib/python3.8/subprocess.py", line 1892, in _communicate
    self.wait(timeout=self._remaining_time(endtime))
  File "/usr/local/lib/python3.8/subprocess.py", line 1079, in wait
    return self._wait(timeout=timeout)
  File "/usr/local/lib/python3.8/subprocess.py", line 1796, in _wait
    raise TimeoutExpired(self.args, timeout)
subprocess.TimeoutExpired: Command '['/usr/local/bin/python', '-c', 'import time; time.sleep(2)']' timed out after 0.9997982999999522 seconds
```

The subprocess we tried to run used the [`time.sleep` function](https://docs.python.org/3/library/time.html#time.sleep) to sleep for `2` seconds. However, we passed the `timeout=1` keyword argument to `subprocess.run` to time out our subprocess after `1` second. This explains why our call to `subprocess.run` ultimately raised a `subprocess.TimeoutExpired` exception.

Note that the `timeout` keyword argument to `subprocess.run` is approximate. Python will make a best effort to kill the subprocess after the `timeout` number of seconds, but it won't necessarily be exact.

## Passing Input to Programs

Sometimes programs expect input to be passed to them via `stdin`.

The `input` keyword argument to `subprocess.run` allows you to pass data to the `stdin` of the subprocess. For example:

```python
import subprocess
import sys

result = subprocess.run(
    [sys.executable, "-c", "import sys; print(sys.stdin.read())"], input=b"underwater"
)
```

We'll receive output like the following after running this code:

```
underwater
```

In this case, we passed the bytes `underwater` to `input`. Our target subprocess used [`sys.stdin`](https://docs.python.org/3/library/sys.html#sys.stdin) to read the passed in `stdin` (`underwater`) and printed it out in our output.

The `input` keyword argument can be useful if you want to chain multiple `subprocess.run` calls together passing the output of one program as the input to another.

## Conclusion

The `subprocess` module is a powerful part of the Python standard library that lets you run external programs and inspect their outputs easily. In this tutorial, you have learned to use `subprocess.run` to control external programs, pass input to them, parse their output, and check their return codes.

The `subprocess` module exposes additional classes and utilities that we did not cover in this tutorial. Now that you have a baseline, you can use [the `subprocess` module's documentation](https://docs.python.org/3/library/subprocess.html) to learn more about other available classes and utilities.

*Editor: Kathryn Hancox*
