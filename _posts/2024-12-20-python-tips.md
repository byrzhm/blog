---
title: Python Tips
date: 2024-12-20 21:59:00 +0800
categories: [Python, tips]
tags: [python]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

## `**` operator

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

## References

- [`*args` and `**kwargs`](https://book.pythontips.com/en/latest/args_and_kwargs.html)
