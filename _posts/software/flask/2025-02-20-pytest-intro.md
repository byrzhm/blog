---
title: Introduction to Pytest
date: 2025-02-20 13:10:00 +0800
categories: [Software, Flask]
tags: [pytest]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

pytest is a powerful, easy-to-use testing framework for Python. It is widely used in unit testing, functional testing, and even integration testing. With its automatic test discovery, fixture system, and powerful assertion handling, pytest makes testing simple, readable, and scalable.

## Why Use pytest?

- ✅ **Simple syntax** – No need to extend test classes; plain functions work.
- ✅ **Auto-discovery** – Finds test files automatically based on naming conventions.
- ✅ **Powerful assertions** – Uses Python’s built-in assert with detailed error reporting.
- ✅ **Fixture system** – Provides reusable setup and teardown logic.
- ✅ **Parameterized tests** – Run the same test with multiple inputs.
- ✅ **Extensive plugin ecosystem** – Supports parallel execution, coverage reports, and mocking.

### 1. Installing pytest

To install pytest, use:
```bash
pip install pytest
```

Check if pytest is installed correctly:
```bash
pytest --version
```

### 2. Writing and Running Tests

A basic pytest test is a function that starts with `test_`.

#### Example: Basic Test

Create a file called `test_example.py`:
```python
# test_example.py
def add(x, y):
    return x + y

def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0
    assert add(0, 0) == 0
```

Run the test with:
```bash
pytest
```

✅ Output:
```
collected 1 item

test_example.py .                                    [100%]

1 passed in 0.01s
```

✅ pytest automatically discovers `test_*.py` files and runs all functions that start with `test_`.

### 3. Using Assertions in pytest

Unlike `unittest`, `pytest` does not require `self.assertEqual`. You can simply use Python’s `assert` statement.

```python
def test_numbers():
    assert (2 + 2) == 4
    assert "pytest".upper() == "PYTEST"
    assert len([1, 2, 3]) == 3
```

**Handling Exceptions**

To check if a function raises an error, use `pytest.raises`:

```python
import pytest

def divide(x, y):
    return x / y

def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError):
        divide(1, 0)
```

### 4. Grouping Tests with Classes

You can organize related tests using classes:

```python
class TestMathOperations:
    def test_add(self):
        assert (3 + 2) == 5

    def test_subtract(self):
        assert (5 - 2) == 3
```

> **Note**: Class names should not start with Test unless they contain test functions.
{: .prompt-warning }

Run specific tests:

```bash
pytest -k "test_add"
```

### 5. Using Fixtures (Test Setup & Teardown)

Fixtures are used to set up test environments. They allow reusable test setup and cleanup.

#### Example: Basic Fixture

```python
import pytest

@pytest.fixture
def sample_data():
    return {"name": "Alice", "age": 25}

def test_data(sample_data):
    assert sample_data["name"] == "Alice"
    assert sample_data["age"] == 25
```

✅ `sample_data` is injected into `test_data`, avoiding repetitive setup code.

#### Example: Fixtures with yield (Teardown)

```python
import pytest

@pytest.fixture
def setup_teardown():
    print("\nSetup: Start Database Connection")
    yield
    print("Teardown: Close Database Connection")

def test_example(setup_teardown):
    print("Running Test")
    assert 1 + 1 == 2
```

Run using command:
```bash
pytest -s # or
pytest --capture=tee-sys
```

✅ Output:

```
Setup: Start Database Connection
Running Test
Teardown: Close Database Connection
```

> `yield` ensures that cleanup code runs after the test finishes.
{: .prompt-info }

### 6. Running Multiple Test Cases (Parameterized Testing)

Instead of writing multiple similar tests, use @pytest.mark.parametrize to run the same test with different inputs.

```python
import pytest

@pytest.mark.parametrize("x, y, result", [(2, 3, 5), (-1, 1, 0), (0, 0, 0)])
def test_addition(x, y, result):
    assert x + y == result
```

✅ Runs the test three times, once for each set of inputs.

### 7. Running Tests Selectively

Run a specific test file:
```bash
pytest test_example.py
```

Run a specific test function:
```bash
pytest test_example.py::test_add
```

Run tests matching a keyword:
```bash
pytest -k "addition"
```

Run tests with detailed output:
```bash
pytest -v
```

Run tests and stop at first failure:
```bash
pytest -x
```

Run tests in parallel:
```bash
pip install pytest-xdist
pytest -n 4  # Runs tests using 4 CPU cores
```

### 8. Marking and Skipping Tests

Skip a test:
```python
import pytest

@pytest.mark.skip(reason="Skipping this test temporarily")
def test_skip():
    assert 1 + 1 == 2
```

Run only selected tests (`@pytest.mark`)

```python
@pytest.mark.slow
def test_large_computation():
    assert sum(range(1000000)) > 0
```

Run only “slow” tests:
```bash
pytest -m slow
```

### 9. Measuring Code Coverage

To check how much of your code is covered by tests:

1. Install `pytest-cov`:
```bash
pip install pytest-cov
```

2. Run tests with coverage:
```bash
pytest --cov=my_package
```

### 10. Debugging Failing Tests

Use `-s` to print output:
```bash
pytest -s
```

Use `--pdb` to enter debug mode on failure:
```bash
pytest --pdb
```

## Conclusion

🔥 pytest makes Python testing simple, powerful, and scalable. Its fixtures, assertions, and parameterization features enable clean and efficient testing workflows.

**Key Takeaways**
- Install with `pip install pytest`
- Write test functions in `test_*.py`
- Use `assert` statements for validation
- Use `fixture`s for reusable setup/teardown
- Use `parameterize`d tests for multiple test cases
- Run selective tests with `-k` or `-m`
- Use `pytest-cov` for test coverage

💡 Whether you’re testing small functions or large applications, pytest is the best tool for the job! 🚀
