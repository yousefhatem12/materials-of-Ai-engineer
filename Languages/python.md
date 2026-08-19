# Python Programming — From Basics to Advanced

A complete reference guide to the Python programming language, covering fundamentals through advanced topics.

## Table of Contents

1. [Introduction](#1-introduction)
2. [Setup & Environment](#2-setup--environment)
3. [Basics](#3-basics)
4. [Control Flow](#4-control-flow)
5. [Data Structures](#5-data-structures)
6. [Functions](#6-functions)
7. [Object-Oriented Programming](#7-object-oriented-programming)
8. [Modules & Packages](#8-modules--packages)
9. [File Handling](#9-file-handling)
10. [Error Handling](#10-error-handling)
11. [Intermediate Concepts](#11-intermediate-concepts)
12. [Advanced Concepts](#12-advanced-concepts)
13. [Concurrency & Parallelism](#13-concurrency--parallelism)
14. [Testing](#14-testing)
15. [Packaging & Virtual Environments](#15-packaging--virtual-environments)
16. [Performance & Best Practices](#16-performance--best-practices)
17. [Useful Standard Library Modules](#17-useful-standard-library-modules)
18. [Resources](#18-resources)

---

## 1. Introduction

Python is a high-level, interpreted, general-purpose programming language known for readability and simplicity. It supports multiple paradigms: procedural, object-oriented, and functional programming.

**Why Python?**
- Simple, readable syntax
- Huge standard library ("batteries included")
- Massive ecosystem (web dev, data science, automation, AI/ML, scripting)
- Cross-platform

---

## 2. Setup & Environment

### Install Python

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install python3 python3-pip

# Check version
python3 --version
```

### Run Python

```bash
python3            # interactive shell (REPL)
python3 script.py  # run a script
```

### Recommended tools
- **IDE/Editor**: VS Code, PyCharm
- **Version manager**: `pyenv` (manage multiple Python versions)
- **Formatter/Linter**: `black`, `flake8`, `ruff`

---

## 3. Basics

### Hello World

```python
print("Hello, World!")
```

### Variables & Types

```python
name = "Alice"          # str
age = 30                 # int
height = 1.75             # float
is_active = True          # bool
nothing = None             # NoneType

print(type(age))  # <class 'int'>
```

### Basic Operators

```python
# Arithmetic
x = 10 + 3     # 13
y = 10 - 3     # 7
z = 10 * 3     # 30
d = 10 / 3     # 3.333... (float division)
fd = 10 // 3   # 3 (floor division)
m = 10 % 3     # 1 (modulo)
p = 2 ** 5     # 32 (power)

# Comparison
10 == 10   # True
10 != 5    # True
10 > 5     # True

# Logical
True and False   # False
True or False    # True
not True          # False
```

### Strings

```python
s = "Hello, Python!"
s.upper()          # "HELLO, PYTHON!"
s.lower()           # "hello, python!"
s.replace("Hello", "Hi")  # "Hi, Python!"
s.split(",")        # ['Hello', ' Python!']
len(s)               # 14
s[0]                  # 'H'
s[0:5]                 # 'Hello'
s[::-1]                 # reversed string

# f-strings (formatted strings)
name = "Bob"
print(f"Hello, {name}!")
```

### Type Conversion

```python
int("42")        # 42
str(42)            # "42"
float("3.14")        # 3.14
bool(0)                # False
list("abc")             # ['a', 'b', 'c']
```

### Input

```python
name = input("What's your name? ")
print(f"Hi, {name}")
```

---

## 4. Control Flow

### If / Elif / Else

```python
age = 20

if age < 13:
    print("Child")
elif age < 20:
    print("Teenager")
else:
    print("Adult")
```

### Loops

```python
# For loop
for i in range(5):
    print(i)  # 0 1 2 3 4

for char in "abc":
    print(char)

# While loop
count = 0
while count < 5:
    print(count)
    count += 1

# Loop control
for i in range(10):
    if i == 3:
        continue   # skip this iteration
    if i == 7:
        break        # exit loop
    print(i)
```

### Ternary Expression

```python
status = "adult" if age >= 18 else "minor"
```

### Match Statement (Python 3.10+)

```python
def http_status(code):
    match code:
        case 200:
            return "OK"
        case 404:
            return "Not Found"
        case 500 | 502 | 503:
            return "Server Error"
        case _:
            return "Unknown"
```

---

## 5. Data Structures

### Lists (ordered, mutable)

```python
fruits = ["apple", "banana", "cherry"]
fruits.append("date")
fruits.remove("banana")
fruits[0]              # "apple"
fruits[-1]              # last item
fruits.sort()
len(fruits)

# List comprehension
squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
```

### Tuples (ordered, immutable)

```python
point = (3, 4)
x, y = point  # unpacking
```

### Dictionaries (key-value pairs)

```python
person = {"name": "Alice", "age": 30}
person["age"]              # 30
person["email"] = "a@x.com"  # add key
person.get("phone", "N/A")     # safe access with default
del person["age"]

for key, value in person.items():
    print(key, value)

# Dict comprehension
squares = {x: x**2 for x in range(5)}
```

### Sets (unordered, unique elements)

```python
a = {1, 2, 3}
b = {2, 3, 4}

a | b     # union {1,2,3,4}
a & b     # intersection {2,3}
a - b     # difference {1}
a.add(5)
```

### Nested Structures

```python
data = {
    "users": [
        {"name": "Alice", "roles": ["admin", "editor"]},
        {"name": "Bob", "roles": ["viewer"]}
    ]
}
print(data["users"][0]["roles"][0])  # "admin"
```

---

## 6. Functions

### Basics

```python
def greet(name):
    return f"Hello, {name}!"

greet("Alice")
```

### Default & Keyword Arguments

```python
def power(base, exponent=2):
    return base ** exponent

power(3)          # 9
power(3, 3)         # 27
power(base=2, exponent=10)  # 1024
```

### *args and **kwargs

```python
def total(*numbers):          # variable positional args
    return sum(numbers)

def config(**settings):        # variable keyword args
    for k, v in settings.items():
        print(k, v)

total(1, 2, 3)             # 6
config(debug=True, env="prod")
```

### Lambda Functions

```python
square = lambda x: x ** 2
add = lambda a, b: a + b
```

### Type Hints

```python
def add(a: int, b: int) -> int:
    return a + b
```

### Docstrings

```python
def divide(a, b):
    """Divide a by b and return the result.

    Args:
        a (float): numerator
        b (float): denominator

    Returns:
        float: result of division
    """
    return a / b
```

---

## 7. Object-Oriented Programming

### Classes & Objects

```python
class Dog:
    species = "Canine"  # class attribute

    def __init__(self, name, age):
        self.name = name   # instance attribute
        self.age = age

    def bark(self):
        return f"{self.name} says Woof!"

d = Dog("Rex", 3)
print(d.bark())
```

### Inheritance

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        raise NotImplementedError

class Cat(Animal):
    def speak(self):
        return f"{self.name} says Meow"

c = Cat("Whiskers")
print(c.speak())
```

### Special (Dunder) Methods

```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __repr__(self):
        return f"Point({self.x}, {self.y})"

    def __add__(self, other):
        return Point(self.x + other.x, self.y + other.y)

    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

p1 = Point(1, 2)
p2 = Point(3, 4)
print(p1 + p2)  # Point(4, 6)
```

### Encapsulation

```python
class Account:
    def __init__(self, balance):
        self._balance = balance      # convention: "protected"
        self.__pin = 1234              # name-mangled: "private"

    @property
    def balance(self):
        return self._balance

    @balance.setter
    def balance(self, value):
        if value < 0:
            raise ValueError("Balance can't be negative")
        self._balance = value
```

### Class Methods & Static Methods

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    @classmethod
    def from_diameter(cls, diameter):
        return cls(diameter / 2)

    @staticmethod
    def is_valid_radius(r):
        return r > 0
```

### Abstract Base Classes

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Square(Shape):
    def __init__(self, side):
        self.side = side

    def area(self):
        return self.side ** 2
```

---

## 8. Modules & Packages

### Importing

```python
import math
from math import sqrt
from math import sqrt as square_root
import numpy as np
```

### Creating a Module

```python
# mymodule.py
def greet(name):
    return f"Hi {name}"
```

```python
# main.py
import mymodule
mymodule.greet("Bob")
```

### Packages

```
myproject/
├── mypackage/
│   ├── __init__.py
│   ├── module_a.py
│   └── module_b.py
└── main.py
```

```python
from mypackage import module_a
```

---

## 9. File Handling

```python
# Writing
with open("file.txt", "w") as f:
    f.write("Hello, file!")

# Reading
with open("file.txt", "r") as f:
    content = f.read()

# Reading line by line
with open("file.txt", "r") as f:
    for line in f:
        print(line.strip())

# Appending
with open("file.txt", "a") as f:
    f.write("\nMore text")
```

### Working with JSON

```python
import json

data = {"name": "Alice", "age": 30}

with open("data.json", "w") as f:
    json.dump(data, f)

with open("data.json", "r") as f:
    loaded = json.load(f)
```

### Working with CSV

```python
import csv

with open("data.csv", "w", newline="") as f:
    writer = csv.writer(f)
    writer.writerow(["name", "age"])
    writer.writerow(["Alice", 30])

with open("data.csv", "r") as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)
```

---

## 10. Error Handling

```python
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"Error: {e}")
except (TypeError, ValueError) as e:
    print(f"Bad input: {e}")
else:
    print("No errors occurred")
finally:
    print("This always runs")
```

### Raising Exceptions

```python
def withdraw(balance, amount):
    if amount > balance:
        raise ValueError("Insufficient funds")
    return balance - amount
```

### Custom Exceptions

```python
class InsufficientFundsError(Exception):
    """Raised when withdrawal exceeds balance."""
    pass

raise InsufficientFundsError("Not enough money")
```

---

## 11. Intermediate Concepts

### Iterators & Generators

```python
# Generator function
def countdown(n):
    while n > 0:
        yield n
        n -= 1

for num in countdown(5):
    print(num)

# Generator expression
squares = (x**2 for x in range(10))  # lazy evaluation
```

### Decorators

```python
import functools
import time

def timer(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        print(f"{func.__name__} took {time.time() - start:.4f}s")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)

slow_function()
```

### Context Managers

```python
class ManagedFile:
    def __init__(self, filename):
        self.filename = filename

    def __enter__(self):
        self.file = open(self.filename, "w")
        return self.file

    def __exit__(self, exc_type, exc_val, exc_tb):
        self.file.close()

with ManagedFile("log.txt") as f:
    f.write("Logging...")

# Using contextlib
from contextlib import contextmanager

@contextmanager
def managed_resource():
    print("Acquiring resource")
    yield "resource"
    print("Releasing resource")

with managed_resource() as r:
    print(f"Using {r}")
```

### Comprehensions (Advanced)

```python
matrix = [[1, 2], [3, 4], [5, 6]]
flat = [num for row in matrix for num in row]  # [1,2,3,4,5,6]

# Nested comprehension
transposed = [[row[i] for row in matrix] for i in range(2)]
```

### Unpacking

```python
a, b, *rest = [1, 2, 3, 4, 5]  # a=1, b=2, rest=[3,4,5]

def merge(*dicts):
    result = {}
    for d in dicts:
        result |= d  # merge operator (3.9+)
    return result
```

---

## 12. Advanced Concepts

### Metaclasses

```python
class Meta(type):
    def __new__(cls, name, bases, dct):
        print(f"Creating class {name}")
        return super().__new__(cls, name, bases, dct)

class MyClass(metaclass=Meta):
    pass
```

### Descriptors

```python
class PositiveNumber:
    def __set_name__(self, owner, name):
        self.name = "_" + name

    def __get__(self, instance, owner):
        return getattr(instance, self.name)

    def __set__(self, instance, value):
        if value < 0:
            raise ValueError("Must be positive")
        setattr(instance, self.name, value)

class Product:
    price = PositiveNumber()

    def __init__(self, price):
        self.price = price
```

### Data Classes

```python
from dataclasses import dataclass, field

@dataclass
class Point:
    x: int
    y: int
    tags: list = field(default_factory=list)

p = Point(1, 2)
print(p)  # Point(x=1, y=2, tags=[])
```

### Enums

```python
from enum import Enum, auto

class Color(Enum):
    RED = auto()
    GREEN = auto()
    BLUE = auto()

print(Color.RED)       # Color.RED
print(Color.RED.name)   # "RED"
print(Color.RED.value)   # 1
```

### Type Hints (Advanced)

```python
from typing import Optional, Union, List, Dict, Callable, TypeVar, Generic

def find_user(id: int) -> Optional[str]:
    ...

Number = Union[int, float]

def apply(func: Callable[[int], int], value: int) -> int:
    return func(value)

T = TypeVar("T")

class Stack(Generic[T]):
    def __init__(self) -> None:
        self.items: List[T] = []

    def push(self, item: T) -> None:
        self.items.append(item)

    def pop(self) -> T:
        return self.items.pop()
```

### Functional Programming Tools

```python
from functools import reduce, partial, lru_cache

# map / filter / reduce
nums = [1, 2, 3, 4, 5]
doubled = list(map(lambda x: x * 2, nums))
evens = list(filter(lambda x: x % 2 == 0, nums))
total = reduce(lambda a, b: a + b, nums)

# partial application
def multiply(a, b):
    return a * b

double = partial(multiply, 2)
double(5)  # 10

# memoization
@lru_cache(maxsize=None)
def fib(n):
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)
```

### Closures

```python
def make_multiplier(factor):
    def multiplier(x):
        return x * factor
    return multiplier

times3 = make_multiplier(3)
times3(10)  # 30
```

### Magic Methods for Iteration

```python
class Range:
    def __init__(self, start, end):
        self.start, self.end = start, end

    def __iter__(self):
        self.current = self.start
        return self

    def __next__(self):
        if self.current >= self.end:
            raise StopIteration
        value = self.current
        self.current += 1
        return value

for i in Range(0, 5):
    print(i)
```

### Async Programming

```python
import asyncio

async def fetch_data(delay, name):
    await asyncio.sleep(delay)
    return f"Data from {name}"

async def main():
    results = await asyncio.gather(
        fetch_data(1, "A"),
        fetch_data(2, "B"),
    )
    print(results)

asyncio.run(main())
```

---

## 13. Concurrency & Parallelism

```python
import threading
import multiprocessing
import concurrent.futures

# Threading (good for I/O-bound tasks)
def worker():
    print("Thread working")

t = threading.Thread(target=worker)
t.start()
t.join()

# Multiprocessing (good for CPU-bound tasks)
def cpu_task(n):
    return sum(i * i for i in range(n))

if __name__ == "__main__":
    with multiprocessing.Pool(4) as pool:
        results = pool.map(cpu_task, [1000000] * 4)

# ThreadPoolExecutor / ProcessPoolExecutor
with concurrent.futures.ThreadPoolExecutor() as executor:
    futures = [executor.submit(worker) for _ in range(5)]
    for f in futures:
        f.result()
```

**Note:** Python has a Global Interpreter Lock (GIL), so threads don't give true parallelism for CPU-bound work — use multiprocessing for that instead.

---

## 14. Testing

### Unittest

```python
import unittest

def add(a, b):
    return a + b

class TestMath(unittest.TestCase):
    def test_add(self):
        self.assertEqual(add(2, 3), 5)

    def test_add_negative(self):
        self.assertEqual(add(-1, -1), -2)

if __name__ == "__main__":
    unittest.main()
```

### Pytest (more popular, less boilerplate)

```bash
pip install pytest
```

```python
# test_math.py
def add(a, b):
    return a + b

def test_add():
    assert add(2, 3) == 5

def test_add_negative():
    assert add(-1, -1) == -2
```

```bash
pytest              # run all tests
pytest -v           # verbose output
pytest --cov        # coverage report (needs pytest-cov)
```

### Mocking

```python
from unittest.mock import Mock, patch

@patch("requests.get")
def test_api_call(mock_get):
    mock_get.return_value.status_code = 200
    mock_get.return_value.json.return_value = {"key": "value"}
    # test code that calls requests.get(...)
```

---

## 15. Packaging & Virtual Environments

### Virtual Environments

```bash
python3 -m venv venv          # create
source venv/bin/activate       # activate (Linux/Mac)
venv\Scripts\activate            # activate (Windows)
deactivate                        # exit venv
```

### pip

```bash
pip install requests
pip install -r requirements.txt
pip freeze > requirements.txt
pip uninstall requests
```

### Project Structure (modern)

```
myproject/
├── src/
│   └── mypackage/
│       ├── __init__.py
│       └── core.py
├── tests/
│   └── test_core.py
├── pyproject.toml
├── README.md
└── .gitignore
```

### pyproject.toml (modern packaging)

```toml
[project]
name = "mypackage"
version = "0.1.0"
description = "My awesome package"
dependencies = [
    "requests>=2.28",
]

[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"
```

### Popular Dependency Managers
- `pip` + `venv` (built-in, standard)
- `poetry` (dependency management + packaging)
- `uv` (very fast, modern alternative)

---

## 16. Performance & Best Practices

### PEP 8 Style Guide (highlights)
- 4 spaces per indentation level
- Max line length ~79-99 characters
- `snake_case` for functions/variables, `PascalCase` for classes, `UPPER_CASE` for constants
- Use docstrings for public modules, functions, classes

### Common Best Practices

```python
# Use list/dict/set comprehensions over manual loops when clear
squares = [x**2 for x in range(10)]

# Use enumerate instead of manual counters
for i, value in enumerate(["a", "b", "c"]):
    print(i, value)

# Use zip to iterate multiple sequences together
names = ["Alice", "Bob"]
ages = [30, 25]
for name, age in zip(names, ages):
    print(name, age)

# Use context managers for resources
with open("file.txt") as f:
    data = f.read()

# Prefer f-strings for formatting
print(f"{name} is {age} years old")

# Use is for None checks
if value is None:
    ...
```

### Profiling & Optimization

```bash
python3 -m cProfile script.py     # profile execution
python3 -m timeit "sum(range(100))"  # time a snippet
```

```python
import timeit
timeit.timeit("sum(range(100))", number=10000)
```

### Tools
- **Formatting**: `black`
- **Linting**: `ruff`, `flake8`, `pylint`
- **Type checking**: `mypy`

---

## 17. Useful Standard Library Modules

| Module | Purpose |
|---|---|
| `os` | Interact with the operating system (files, env vars) |
| `sys` | System-specific parameters and functions |
| `datetime` | Dates and times |
| `re` | Regular expressions |
| `random` | Random number generation |
| `collections` | Specialized containers (`Counter`, `defaultdict`, `deque`, `namedtuple`) |
| `itertools` | Efficient looping tools (`chain`, `product`, `combinations`) |
| `pathlib` | Object-oriented filesystem paths |
| `logging` | Application logging |
| `argparse` | Command-line argument parsing |
| `subprocess` | Run shell commands |
| `json` | JSON encode/decode |
| `unittest` / `pytest` | Testing |
| `asyncio` | Asynchronous programming |

### Quick Examples

```python
from collections import Counter, defaultdict, namedtuple

Counter("mississippi")          # Counter({'i': 4, 's': 4, 'p': 2, 'm': 1})

d = defaultdict(list)
d["fruits"].append("apple")      # no KeyError

Point = namedtuple("Point", ["x", "y"])
p = Point(1, 2)
```

```python
import itertools

list(itertools.combinations([1, 2, 3], 2))  # [(1,2), (1,3), (2,3)]
list(itertools.product([1, 2], ["a", "b"]))   # [(1,'a'), (1,'b'), (2,'a'), (2,'b')]
```

```python
from pathlib import Path

p = Path("data") / "file.txt"
p.exists()
p.read_text()
p.parent
```

---

## 18. Resources

- [Official Python Documentation](https://docs.python.org/3/)
- [PEP 8 – Style Guide](https://peps.python.org/pep-0008/)
- [Real Python Tutorials](https://realpython.com/)
- [Python Package Index (PyPI)](https://pypi.org/)

---

**Happy coding! 🐍**
