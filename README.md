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

## 4. Where should prompts go?

There is some debate about whether prompts (`input()`) belong inside or outside a function. The recommended approach is to keep the prompt **outside** the function and pass the value in as an argument. This keeps the function reusable and testable, because it works the same whether the value came from the user, a file, or another function.

```python
def add(a: int, b: int) -> int:
    return a + b

a = int(input("Enter first number: "))
b = int(input("Enter second number: "))
print(add(a, b))
```

## 5. Type Hinting

Type hinting is described in `PEP 484` and is widely used in production code. It tells readers and tools what types a function expects and returns.

```python
def add(a: int, b: int) -> int:
    return a + b
```

Type hints are **not enforced at runtime**: if they are violated, Python still runs normally. Instead, type checkers such as `mypy` and editors using Pylance highlight the mismatch before you run the code.

---

## 6. `return` vs `print`

`print()` shows a value on the screen. `return` hands a value back to whoever called the function. A function with no `return` gives back `None`.

````python
def add_print(a, b):
    print(a + b)       # shows 7, gives back None

def add_return(a, b):
    return a + b       # shows nothing, gives back 7

x = add_print(3, 4)    # x is None
y = add_return(3, 4)   # y is 7
````

A function stops the moment it hits `return`, so any code after it in the same block never runs.

## 7. Scope

Variables created inside a function are **local**: they exist only while the function runs.

````python
def sum_num(*nums):
    return sum(nums)

sum_num(1, 2, 3)
print(nums)   # NameError: nums only existed inside the function
````

To use something outside, `return` it and store it in a variable.

## 8. The mutable default argument trap

Default values are created **once**, when the function is defined, not on every call.

````python
def add_item(item, basket=[]):    # dangerous
    basket.append(item)
    return basket

add_item("apple")     # ['apple']
add_item("banana")    # ['apple', 'banana']  <- the same list is reused
````

The fix is to default to `None` and create the list inside:

````python
def add_item(item, basket=None):
    if basket is None:
        basket = []
    basket.append(item)
    return basket
````

## 9. Lambda functions

A lambda is a small **anonymous** function written in one line.

````python
lambda arguments: expression
````

It is equivalent to a `def` whose body is a single `return`:

````python
def square(x):
    return x ** 2

square = lambda x: x ** 2    # same behaviour
````

$$
\begin{array}{|l|l|l|}
\hline
\textbf{} & \texttt{def} & \texttt{lambda} \\
\hline
\text{Has a name} & \text{Yes} & \text{No (anonymous)} \\
\text{Statements allowed} & \text{Many lines} & \text{One expression only} \\
\text{Needs } \texttt{return} & \text{Yes} & \text{No, the expression is returned} \\
\text{Docstring and type hints} & \text{Yes} & \text{No} \\
\text{Best for} & \text{Reusable logic} & \text{Short throwaway functions} \\
\hline
\end{array}
$$

### Multiple arguments, defaults, and `*args`

````python
add = lambda a, b: a + b
power = lambda base, exp=2: base ** exp
total = lambda *nums: sum(nums)

print(add(3, 5))        # 8
print(power(5))         # 25
print(power(2, 3))      # 8
print(total(1, 2, 3))   # 6
````

### Conditional expressions

A lambda cannot contain an `if` block, but it can use the one-line `x if condition else y` form:

````python
parity = lambda n: "even" if n % 2 == 0 else "odd"

print(parity(4))   # even
print(parity(7))   # odd
````

### Where lambdas are actually useful

Lambdas shine as a quick argument to another function.

**Sorting with a custom key**

````python
names = ["Lulu", "al", "Bob"]
print(sorted(names, key=lambda s: len(s)))   # ['al', 'Bob', 'Lulu']

students = [("Lulu", 82), ("Bob", 75), ("Amy", 91)]
print(sorted(students, key=lambda s: s[1], reverse=True))
# [('Amy', 91), ('Lulu', 82), ('Bob', 75)]
````

**`map` applies a function to every item**

````python
nums = [1, 2, 3, 4]
print(list(map(lambda x: x * 2, nums)))   # [2, 4, 6, 8]
````

**`filter` keeps items where the function returns True**

````python
print(list(filter(lambda x: x % 2 == 0, range(10))))   # [0, 2, 4, 6, 8]
````

**`apply` on a pandas column**

````python
iris_df["size"] = iris_df["petal length (cm)"].apply(
    lambda x: "long" if x > 4 else "short"
)
````

### Pitfalls

**1. Do not assign a lambda to a name for reuse.** PEP 8 (Ruff rule `E731`) says to use `def` instead, because a named function gets a docstring, type hints, and a readable traceback.

````python
# Avoid
square = lambda x: x ** 2

# Prefer
def square(x: float) -> float:
    """Return x squared."""
    return x ** 2
````

**2. Late binding in loops.** A lambda looks up variables when it **runs**, not when it is created.

````python
funcs = [lambda: i for i in range(3)]
print([f() for f in funcs])    # [2, 2, 2]  <- not [0, 1, 2]

funcs = [lambda i=i: i for i in range(3)]   # bind i now with a default
print([f() for f in funcs])    # [0, 1, 2]
````

**3. Keep it short.** If a lambda needs more than one simple expression, write a `def`.

### Rule of thumb

Use a lambda when the function is used **once, inline**, as an argument (`key=`, `map`, `filter`, `apply`). Use `def` for everything you will name, reuse, test, or document.

## 10. Error handling: raise and try/except

A function that detects bad input should **raise** an error with a clear message. The caller decides whether to **catch** it.

````python
def speed(distance: float, time: float) -> float:
    """Return distance / time. Raises ValueError if time is not positive."""
    if time <= 0:
        raise ValueError("Time must be greater than zero.")
    return distance / time


try:
    print(speed(100, 2))    # 50.0
    print(speed(50, 0))     # raises ValueError
except ValueError as e:
    print("Error:", e)
````

Validate types the same way:

````python
def sum_numbers(*nums: float) -> float:
    """Return the sum of all numbers. Raises TypeError for non-numbers."""
    for n in nums:
        if not isinstance(n, (int, float)):
            raise TypeError(f"Expected numbers, got {type(n).__name__}: {n!r}")
    return sum(nums)
````

Catch several error types with a tuple, and use `type(e).__name__` to see which one occurred:

````python
try:
    print(sum_numbers(1, 2, "3"))
except (TypeError, ValueError) as e:
    print(f"{type(e).__name__}: {e}")
# TypeError: Expected numbers, got str: '3'
````

$$
\begin{array}{|l|l|}
\hline
\textbf{Do} & \textbf{Avoid} \\
\hline
\texttt{raise} \text{ inside the function} & \text{Printing the error inside the function} \\
\text{Catch specific errors (} \texttt{except ValueError}) & \text{Bare } \texttt{except:} \text{ that hides real bugs} \\
\texttt{try/except} \text{ at the edge (prompt, script)} & \text{Wrapping every function in } \texttt{try/except} \\
\hline
\end{array}
$$

## 11. Summary

$$
\begin{array}{|l|l|}
\hline
\textbf{Concept} & \textbf{Key idea} \\
\hline
\texttt{def} \text{ vs calling} & \text{Defining registers the function, } \texttt{()} \text{ runs it} \\
\texttt{return} \text{ vs } \texttt{print} & \text{return gives data back, print only displays it} \\
\text{Scope} & \text{Local variables disappear when the function ends} \\
\text{Mutable defaults} & \text{Use } \texttt{None} \text{ and create the object inside} \\
\text{Lambda} & \text{Anonymous one-expression function for inline use} \\
\texttt{raise} \text{ and } \texttt{try/except} & \text{Raise where detected, catch where you can recover} \\
\hline
\end{array}
````
````

Sections still worth adding once you cover them: pytest, logging, moving functions into a `.py` module, and decorators.

