# Production Python: Functions as the Key Building Block

Writing awesome code is every developer's dream — not writing spaghetti code is one of mine. In a moment of self-reflection, I decided to go back to the drawing board and admit I still write code like a rookie, and I needed to change that, especially if I wanted to get into production Python.

My starting point was functions. It sounds lame — until you write code you actually love, especially for repeated tasks, and feel how much easier it is to work with.

This is a full walkthrough, from basic function creation up to intermediate concepts.

## 1. Defining a function

We start with the `def` keyword, which tells Python to expect a user-defined function. Function names follow the same rules as variable naming.

```python
def greet():
    print("Hello!")
```

## 2. Parameters and arguments

A function takes **parameters** — these can be default or flexible. The actual values passed in when calling the function are called **arguments**.

```python
def add(a, b):      # a, b are parameters
    return a + b

add(3, 5)             # 3, 5 are arguments
```

## 3. Positional and keyword arguments

How arguments are passed defines them as **positional** (matched by order) or **keyword** (matched by name).

```python
def describe(name, age, city):
    return f"{name}, {age}, from {city}"

describe("Lulu", 25, "Nairobi")                   # positional
describe(age=25, city="Nairobi", name="Lulu")      # keyword
```

## 4. `*args` and `**kwargs`

This naturally leads to flexible arguments — for when you don't know in advance how many values will be passed in.

```python
def total(*args):          # collects extra positional arguments into a tuple
    return sum(args)

def profile(**kwargs):     # collects extra keyword arguments into a dict
    return kwargs
```

---

*More sections coming as the walkthrough progresses — scope, type hints, pure functions, and classes.*