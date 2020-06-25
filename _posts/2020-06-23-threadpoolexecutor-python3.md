---
layout: post
title: How To Use ThreadPoolExecutor in Python 3
date:  2020-06-23 06:00:00 -0700
categories: posts
---

# {{ page.title }}

<small style="font-weight: 175;">{{ page.date | date: "%-d %B, %Y"}}</small>

*This article was [originally published in DigitalOcean’s public knowledge base](https://www.digitalocean.com/community/tutorials/how-to-use-threadpoolexecutor-in-python-3). It has been reproduced here with some minor edits.*

### Introduction

[Python](https://www.python.org/) _threads_ are a form of parallelism that allow your program to run multiple procedures at once. Parallelism in Python can also be achieved using multiple processes, but threads are particularly well suited to speeding up applications that involve significant amounts of I/O (input/output).

Example [I/O-bound operations](https://en.wikipedia.org/wiki/I/O_bound#:~:text=In%20computer%20science%2C%20I%2FO,a%20task%20being%20CPU%20bound.) include making web requests and reading data from files. In contrast to I/O-bound operations, [CPU-bound operations](https://en.wikipedia.org/wiki/CPU-bound) (like performing math with the Python standard library) will not benefit much from Python threads.

Python 3 includes the `ThreadPoolExecutor` utility for executing code in a thread.

In this tutorial, we will use `ThreadPoolExecutor` to make network requests expediently. We'll define a function well suited for invocation within threads, use `ThreadPoolExecutor` to execute that function, and process results from those executions.

For this tutorial, we’ll make network requests to check for the existence of [Wikipedia](https://en.wikipedia.org/wiki/Main_Page) pages.


> **Note:** The fact that I/O-bound operations benefit more from threads than CPU-bound operations is caused by an idiosyncrasy in Python called the, [_global interpreter lock_](https://wiki.python.org/moin/GlobalInterpreterLock). If you'd like, you can learn more about Python's global interpreter lock [in the official Python documentation](https://wiki.python.org/moin/GlobalInterpreterLock).

## Prerequisites

To get the most out of this tutorial, it is recommended to have some familiarity with programming in Python and a local Python programming environment with `requests` installed.

You can review these tutorials for the necessary background information:

- [How to Code in Python 3](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-python-3)
- [How To Install Python 3 and Set Up a Local Programming Environment on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-local-programming-environment-on-ubuntu-18-04)

- To install the `requests` package into your [local Python programming environment](https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-local-programming-environment-on-ubuntu-18-04), you can run this command:

```command
pip install --user requests==2.23.0
```

## Step 1 — Defining a Function to Execute in Threads

Let's start by defining a function that we'd like to execute with the help of threads.

Using `nano` or your preferred text editor/development environment, you can open this file:

```command
nano wiki_page_function.py
```

For this tutorial, we'll write a function that determines whether or not a Wikipedia page exists:

```python
# wiki_page_function.py
import requests

def get_wiki_page_existence(wiki_page_url, timeout=10):
    response = requests.get(url=wiki_page_url, timeout=timeout)

    page_status = "unknown"
    if response.status_code == 200:
        page_status = "exists"
    elif response.status_code == 404:
        page_status = "does not exist"

    return wiki_page_url + " - " + page_status
```

The `get_wiki_page_existence` [function](https://www.digitalocean.com/community/tutorials/how-to-define-functions-in-python-3) accepts two arguments: a URL to a Wikipedia page (`wiki_page_url`), and a `timeout` number of seconds to wait for a response from that URL.

`get_wiki_page_existence` uses the [`requests`](https://requests.readthedocs.io/en/master/) package to make a web request to that URL. Depending on the [status code](https://www.digitalocean.com/community/tutorials/how-to-troubleshoot-common-http-error-codes) of the HTTP `response`, a string is returned that describes whether or not the page exists. Different status codes represent different outcomes of a HTTP request. This procedure assumes that a `200` "success" status code means the Wikipedia page exists, and a `404` "not found" status code means the Wikipedia page does not exist.

As described in the Prerequisites section, you'll need the `requests` package installed to run this function.

Let's try running the function by adding the `url` and function call following the `get_wiki_page_existence` function:

```python
# wiki_page_function.py
. . .
url = "https://en.wikipedia.org/wiki/Ocean"
print(get_wiki_page_existence(wiki_page_url=url))
```

Once you've added the code, save and close the file.

If we run this code:

```command
python wiki_page_function.py
```

We'll see output like the following:

```
https://en.wikipedia.org/wiki/Ocean - exists
```

Calling the `get_wiki_page_existence` function with a valid Wikipedia page returns a string that confirms the page does, in fact, exist.


> **Warning:** In general, it is not safe to share Python objects or state between threads without taking special care to avoid concurrency bugs. When defining a function to execute in a thread, it is best to define a function that performs a single job and does not share or publish state to other threads. `get_wiki_page_existence` is an example of such a function.

## Step 2 — Using ThreadPoolExecutor to Execute a Function in Threads

Now that we have a function well suited to invocation with threads, we can use `ThreadPoolExecutor` to perform multiple invocations of that function expediently.

Let's modify `wiki_page_function.py` so it now reads like this:

```python
# wiki_page_function.py
import requests
import concurrent.futures

def get_wiki_page_existence(wiki_page_url, timeout=10):
    response = requests.get(url=wiki_page_url, timeout=timeout)

    page_status = "unknown"
    if response.status_code == 200:
        page_status = "exists"
    elif response.status_code == 404:
        page_status = "does not exist"

    return wiki_page_url + " - " + page_status

wiki_page_urls = [
    "https://en.wikipedia.org/wiki/Ocean",
    "https://en.wikipedia.org/wiki/Island",
    "https://en.wikipedia.org/wiki/this_page_does_not_exist",
    "https://en.wikipedia.org/wiki/Shark",
]
with concurrent.futures.ThreadPoolExecutor() as executor:
    futures = []
    for url in wiki_page_urls:
        futures.append(
            executor.submit(get_wiki_page_existence, wiki_page_url=url)
        )
    for future in concurrent.futures.as_completed(futures):
        print(future.result())
```
Let's take a look at how this code works:

- `concurrent.futures` is imported to give us access to `ThreadPoolExecutor`.
- A `with` statement is used to create a `ThreadPoolExecutor` instance `executor` that will promptly clean up threads upon completion.
- Four jobs are `submitted` to the `executor`: one for each of the URLs in the `wiki_page_urls` list.
- Each call to `submit` returns a [`Future` instance](https://docs.python.org/3/library/concurrent.futures.html#concurrent.futures.Future) that is stored in the `futures` list.
- The `as_completed` function waits for each `Future` `get_wiki_page_existence` call to complete so we can print its result.

If we run this program again, with the following command:

```command
python wiki_page_function.py
```

We'll see output like the following:

```
https://en.wikipedia.org/wiki/Island - exists
https://en.wikipedia.org/wiki/Ocean - exists
https://en.wikipedia.org/wiki/this_page_does_not_exist - does not exist
https://en.wikipedia.org/wiki/Shark - exists
```

This output makes sense: 3 of the URLs are valid Wikipedia pages, and one of them `this_page_does_not_exist` is not. Note that your output may be ordered differently than this output. The `concurrent.futures.as_completed` function in this example returns results as soon as they are available, regardless of what order the jobs were submitted in.

## Step 3 — Processing Exceptions From Functions Run in Threads

In the previous step, `get_wiki_page_existence` successfully returned a value for all of our invocations. In this step, we'll see that `ThreadPoolExecutor` can also raise exceptions generated in threaded function invocations.

Let's consider the following example code block:

```python
# wiki_page_function.py
import requests
import concurrent.futures


def get_wiki_page_existence(wiki_page_url, timeout=10):
    response = requests.get(url=wiki_page_url, timeout=timeout)

    page_status = "unknown"
    if response.status_code == 200:
        page_status = "exists"
    elif response.status_code == 404:
        page_status = "does not exist"

    return wiki_page_url + " - " + page_status


wiki_page_urls = [
    "https://en.wikipedia.org/wiki/Ocean",
    "https://en.wikipedia.org/wiki/Island",
    "https://en.wikipedia.org/wiki/this_page_does_not_exist",
    "https://en.wikipedia.org/wiki/Shark",
]
with concurrent.futures.ThreadPoolExecutor() as executor:
    futures = []
    for url in wiki_page_urls:
        futures.append(
            executor.submit(
                get_wiki_page_existence, wiki_page_url=url, timeout=0.00001
            )
        )
    for future in concurrent.futures.as_completed(futures):
        try:
            print(future.result())
        except requests.ConnectTimeout:
            print("ConnectTimeout.")
```

This code block is nearly identical to the one we used in Step 2, but it has two key differences:

- We now pass `timeout=0.00001` to `get_wiki_page_existence`. Since the `requests` package won't be able to complete its web request to Wikipedia in `0.00001` seconds, it will raise a `ConnectTimeout` exception.
- We catch `ConnectTimeout` exceptions raised by `future.result()` and print out a string each time we do so.

If we run the program again, we'll see the following output:

```
ConnectTimeout.
ConnectTimeout.
ConnectTimeout.
ConnectTimeout.
```

Four `ConnectTimeout` messages are printed—one for each of our four `wiki_page_urls`, since none of them were able to complete in `0.00001` seconds and each of the four `get_wiki_page_existence` calls raised the `ConnectTimeout` exception.

You've now seen that if a function call submitted to a `ThreadPoolExecutor` raises an exception, then that exception can get raised normally by calling `Future.result`. Calling `Future.result` on all your submitted invocations ensures that your program won't miss any exceptions raised from your threaded function.

## Step 4 — Comparing Execution Time With and Without Threads

Now let's verify that using `ThreadPoolExecutor` actually makes your program faster.

First, let's time `get_wiki_page_existence` if we run it without threads:

```python
# wiki_page_function.py
import time
import requests
import concurrent.futures


def get_wiki_page_existence(wiki_page_url, timeout=10):
    response = requests.get(url=wiki_page_url, timeout=timeout)

    page_status = "unknown"
    if response.status_code == 200:
        page_status = "exists"
    elif response.status_code == 404:
        page_status = "does not exist"

    return wiki_page_url + " - " + page_status

wiki_page_urls = ["https://en.wikipedia.org/wiki/" + str(i) for i in range(50)]

print("Running without threads:")
without_threads_start = time.time()
for url in wiki_page_urls:
    print(get_wiki_page_existence(wiki_page_url=url))
print("Without threads time:", time.time() - without_threads_start)
```

In the code example we call our `get_wiki_page_existence` function with fifty different Wikipedia page URLs one by one. We use the [`time.time()` function](https://docs.python.org/3/library/time.html#time.time) to print out the number of seconds it takes to run our program.

If we run this code again as before, we'll see output like the following:

```
Running without threads:
https://en.wikipedia.org/wiki/0 - exists
https://en.wikipedia.org/wiki/1 - exists
. . .
https://en.wikipedia.org/wiki/48 - exists
https://en.wikipedia.org/wiki/49 - exists
Without threads time: 5.803015232086182
```

Entries 2–47 in this output have been omitted for brevity.

The number of seconds printed after `Without threads time` will be different when you run it on your machine—that's OK, you are just getting a baseline number to compare with a solution that uses `ThreadPoolExecutor`. In this case, it was `~5.803` seconds.

Let's run the same fifty Wikipedia URLs through `get_wiki_page_existence`, but this time using `ThreadPoolExecutor`:

```python
# wiki_page_function.py
import time
import requests
import concurrent.futures


def get_wiki_page_existence(wiki_page_url, timeout=10):
    response = requests.get(url=wiki_page_url, timeout=timeout)

    page_status = "unknown"
    if response.status_code == 200:
        page_status = "exists"
    elif response.status_code == 404:
        page_status = "does not exist"

    return wiki_page_url + " - " + page_status
wiki_page_urls = ["https://en.wikipedia.org/wiki/" + str(i) for i in range(50)]

print("Running threaded:")
threaded_start = time.time()
with concurrent.futures.ThreadPoolExecutor() as executor:
    futures = []
    for url in wiki_page_urls:
        futures.append(
            executor.submit(get_wiki_page_existence, wiki_page_url=url)
        )
    for future in concurrent.futures.as_completed(futures):
        print(future.result())
print("Threaded time:", time.time() - threaded_start)
```

The code is the same code we created in Step 2, only with the addition of some print statements that show us the number of seconds it takes to execute our code.

If we run the program again, we'll see the following:

```
Running threaded:
https://en.wikipedia.org/wiki/1 - exists
https://en.wikipedia.org/wiki/0 - exists
. . .
https://en.wikipedia.org/wiki/48 - exists
https://en.wikipedia.org/wiki/49 - exists
Threaded time: 1.2201685905456543
```

Again, the number of seconds printed after `Threaded time` will be different on your computer (as will the order of your output).

You can now compare the execution time for fetching the fifty Wikipedia page URLs with and without threads.

On the machine used in this tutorial, without threads took `~5.803` seconds, and with threads took `~1.220` seconds. Our program ran significantly faster with threads.

## Conclusion

In this tutorial, you have learned how to use the `ThreadPoolExecutor` utility in Python 3 to efficiently run code that is I/O bound. You created a function well suited to invocation within threads, learned how to retrieve both output and exceptions from threaded executions of that function, and observed the performance boost gained by using threads.

From here you can learn more about other concurrency functions offered by the [`concurrent.futures` module](https://docs.python.org/3/library/concurrent.futures.html).

*Editor: Kathryn Hancox*
