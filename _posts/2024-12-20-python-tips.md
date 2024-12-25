---
title: Python Tips
date: 2024-12-20 21:59:00 +0800
categories: [Programming Languages, Python]
tags: [python]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

## `**` Special Usages

### 1. Unpacking a dictionary into keyword arguments in a function call

```python
def greet(name, age):
    print(f"Hello, {name}, You are {age} years old.")

person = {"name": "Alice", "age": 30}
greet(**person)
```

Output:

```
Hello, Alice. You are 30 years old.
```

### 2. Merging two dictionaries

```python
dict1 = {"a": 1, "b": 2}
dict2 = {"b": 3, "c": 4}

merged_dict = {**dict1, **dict2}
print(merged_dict)
```

Output:

```
{'a': 1, 'b': 3, 'c': 4}
```

> Notice that the overlapping key 'b', its value is overwritten by `dict2`.
{: .prompt-danger }

## `*` Special Usages

### 1. Unpacking Iterables

```python
def add(a, b, c):
    return a + b + c

nums = [1, 2, 3]
result = add(*nums)
print(result)
```

Output:

```
6
```

### 2. Variable-Length Function Arguments

```python
def print_all(*args):
    for arg in args:
        print(arg)

print_all(1, 2, 3, 4)
```

Output:

```
1
2
3
4
```

### 3. Unpacking in Iterables

```python
a = [1, 2, 3]
b = [*a, 4, 5]
print(b)
```

Output:

```
[1, 2, 3, 4, 5]
```

### 4. Keyword-Only Arguments

```python
def func(a, *, b):
    return a + b

# func(1, 2) -> Error, func() takes 1 positional argument but 2 were given
func(1, b=2) # works
```

### 5. List/Iterable Multiplication

```python
repeated = [1, 2, 3] * 3
```

BTW, be careful when using this syntactic sugar because it may create references to the same object.

```python
repeated = [[1, 2, 3]] * 3
print(repeated)
repeated[0][0] = 6
print(repeated)
```

## References

- [`*args` and `**kwargs`](https://book.pythontips.com/en/latest/args_and_kwargs.html)
- [Syntactic Sugar: Why Python Is Sweet and Pythonic](https://realpython.com/syntactic-sugar-python/)
