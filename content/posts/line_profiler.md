+++
date = '2025-09-23T15:17:50Z'
draft = false
title = 'Finding Performance Issues In Python Using Line Profiler'
hideToc = true
+++

## Overview

- Line profiler is a 3rd party library available on [GitHub](https://github.com/pyutils/line_profiler).
- Line profiler package helps in identifying the cause of CPU-bound problems in Python code.
- Gives you a data driven approach on where to allocate your time in order to fix performance issues.
- Understand the output of line_profiler by working through a simple code example.

## Introduction

Python has established itself as a top tier programming language. As of late 2025 according to the [TIOBE](https://www.tiobe.com/tiobe-index/) index, the top 3 languages as a measure of programming language popularity, are consistently Python, C, and C++, often with Python in the lead. This popularity and increase in use of Python has been driven by fields such as machine learning, artificial intelligence and data engineering. As these fields continue to grow and so does their use of Python, the performance of code matters more than ever.

Your Python code is great and is functionally correct, but it may need to run faster. In this blog post, we will use the `line_profiler` package to profile CPU resource usage. Profiling allows us to find bottlenecks in our code so we can identify and correct it, and gain a practical performance gain in our code.

### About Line Profiler Package

The line profiler package helps in identifying the cause of CPU-bound problems in Python code. It works by profiling individual functions on a line-by-line basis. Performance problems can be grouped into CPU, memory, disk IO and network IO bound problems.

A CPU-bound problem is one where your program's speed is limited by how fast the CPU can process instructions, not by waiting for I/O (disk reads, network requests, etc.). Some examples of CPU bound tasks are:

- Complex mathematical calculations - example when using the popular numpy library.
- Data processing/transformations - example using pandas module to filter, aggregate, transform, and join data.
- Image/video encoding for example - example using OpenCV to compress/encode pixels.

### Profiling Efficiently

If your code is running slowly, you do not want to guess as to what the cause of the slowness could be. If you avoid profiling and jump straight into optimizations, then you could end up wasting your time and creating more work for yourself. Profiling takes out the guess work out of where you should allocate your time to introduce performance optimizations and gives you a data driven approach to the process.

Another important thing to remember when profiling is to isolate the part of the system that you need to test. Hopefully your code is modular and organised into their own modules. It is not a good idea to start profiling from the main function, as this would generate a lot of noise in the profiling data. Also, profiling adds overhead (10x to 100x slowdowns) which will affect the system under test.

So avoid doing this:

```python
@profile
def main():
    # implementation

if __name__ == "__main__":
    main()
```

And try to extract a test case which isolates the part of the system you want to test:

```python
@profile
def test_validate_user_returns_true_for_valid_data():
    processor = DataProcessor()
    user_data = {'id': 1, 'name': 'Alice', 'email': 'alice@example.com'}
    result = processor.validate_user(user_data)
    assert result is True
```

## Using Line Profiler

Line profiler is a third party package that you can install with:

```
pip3 install line_profiler
```

### Profiling code

In order to profile code, we need to:

- Import `line_profiler` package.
- Add the `@line_profiler.profile` decorator to the function we want to profile.

Let's do both of those to the code we want to profile:

```python
import random

import line_profiler

def get_random_numbers():
    nums = list()
    upper_bound = 1_000_000
    for _ in range(upper_bound):
        n = random.randint(0, upper_bound)
        nums.append(n)
    return nums

@line_profiler.profile
def flux(nums):
    found = list()
    upper_bound = 1_000_000
    for _ in range(1000):
        n = random.randint(0, upper_bound)
        if n in nums:
            found.append(n)
    return found

nums = get_random_numbers()
flux(nums)
```

Save the code to file `main.py`.

**Note**: The code is contrived, but we want to use a small and complete example that demonstrates how to identify, understand and fix a performance issue.

Let's run the code:

In order to run the code, we need to set the `LINE_PROFILE=1` environment variable as follows:

```
LINE_PROFILE=1 python3 main.py
```

When you run the code, you will see the following output:

```plain
Timer unit: 1e-09 s

  3.45 seconds - main.py:15 - flux
Wrote profile results to profile_output.txt
Wrote profile results to profile_output_2025-12-06T211824.txt
Wrote profile results to profile_output.lprof
To view details run:
python -m line_profiler -rtmz profile_output.lprof
```

The output shows that `line_profiler` has profiled the code and found that the `flux` function (at line 15 in main.py where the decorator is located) took **3.45** seconds to execute. The profiler has saved detailed results to three files:

- profile_output.txt (human-readable)
- profile_output_2025-12-06T211824.txt (timestamped backup)
- profile_output.lprof (binary format)

We can examine the profiling results with the following command:

```
python3 -m line_profiler -rtmz profile_output.lprof
```

The output shows detailed profiling results of the function we added our decorator to:

```plain
Timer unit: 1e-06 s

Total time: 3.44566 s
File: main.py
Function: flux at line 15

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
    15                                           @line_profiler.profile
    16                                           def flux(nums):
    17         1          2.0      2.0      0.0      found = list()
    18         1          0.0      0.0      0.0      upper_bound = 1_000_000
    19      1001        527.0      0.5      0.0      for _ in range(1000):
    20      1000       6827.0      6.8      0.2          n = random.randint(0, upper_bound)
    21      1000    3437855.0   3437.9     99.8          if n in nums:
    22       631        446.0      0.7      0.0              found.append(n)
    23         1          0.0      0.0      0.0      return found

  3.45 seconds - main.py:15 - flux
```

Let's understand what the columns in the profiling data mean:

- **Line #** - Line number in the code.
- **Hits** - How many times that line executed.
- **Time** - Total time spent on that line (in nanoseconds).
- **Per Hit** - Average time per execution of that line.
- **% Time** - Percentage of total function time spent on that line.
- **Line Contents** - The actual code.

In the output of the profiling data, our eye should be immediately drawn to line 21 (`if n in nums:`). The `% Time` shows that this dominates at 99.8% of execution time. Checking if a number is in the list is consuming a lot of time and is contributing immensely to the 3.45 seconds that the function is taking to execute.

### The Fix

Now that we have identified the cause of our codes bottleneck, we have to find out why this is happening.

What the code is doing on every iteration is generating a random number `n` and seeing if that number appears in `nums`. Importantly, `nums` is a list and checking membership in a list is `O(n)`.

O(n) is linear time so we start from the beginning of the list and potentially check every number to the end of the list. In a list with small numbers this would not really be an issue, but we have a list of a million numbers, and if we generate a random number say 23, and 23 is the last element in `nums`, that is a million comparisons. This is inefficient and wasting a lot of CPU time.

A common technique in Python is to convert lists to sets when performing membership testing. Sets in Python are implemented as hash tables, which allow for constant-time (O(1)) lookups on average. This means that checking whether `n` exists in a set is much faster, especially when dealing with sizeable datasets.

Let's update the code to switch `nums` from a list to a set:

```python
def get_random_numbers():
    nums = set()
    upper_bound = 1_000_000
    for _ in range(upper_bound):
        n = random.randint(0, upper_bound)
        nums.add(n)
    return nums
```

### Profiling code change

```plain
LINE_PROFILE=1 python3 main.py
```

Output the profiling data:

```plain
Timer unit: 1e-09 s

  0.00 seconds - main.py:15 - flux
Wrote profile results to profile_output.txt
Wrote profile results to profile_output_2025-12-09T142131.txt
Wrote profile results to profile_output.lprof
To view details run:
python -m line_profiler -rtmz profile_output.lprof
```

```plain
python -m line_profiler -rtmz profile_output.lprof
Timer unit: 1e-06 s

Total time: 0.004835 s
File: main.py
Function: flux at line 15

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
    15                                           @line_profiler.profile
    16                                           def flux(nums):
    17         1          1.0      1.0      0.0      found = list()
    18         1          1.0      1.0      0.0      upper_bound = 1_000_000
    19      1001        277.0      0.3      5.7      for _ in range(1000):
    20      1000       3996.0      4.0     82.6          n = random.randint(0, upper_bound)
    21      1000        402.0      0.4      8.3          if n in nums:
    22       627        158.0      0.3      3.3              found.append(n)
    23         1          0.0      0.0      0.0      return found

  0.00 seconds - main.py:15 - flux
```

A dramatic speed up! The total time spent in the `flux` function has gone from 3.45 seconds to 0.00 seconds!.

Converting `nums` to a set made lookups O(1), this dramatically reduced the time checking for membership.

## Summary:

In this post, we learned:

- How to install and configure `line_profiler` package.
- Run the profiler against our code.
- Understand the output of the profiler.
- Identify and then fix code based on the results output from the profiler.

If you think there is a bottleneck in your code, make sure you use a profiler to understand where the time/resources are being wasted. You need evidence to determine the most efficient ways to make your code run faster.

When writing code, try to keep it simple, readable and correct. This also means making sure you have a decent amount of unit test coverage in your code. Unit tests will help you avoid mistakes and keep your results reproducible. So when you make a change to your code after analysing profiling results, you can run your unit tests after and make sure your code is still functionally correct.

Thank you for reading.
