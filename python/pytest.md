# Unit Testing With Python's Pytest


## Running Tests

Pytest is called from the terminal. Examples of some common actions include:

| Action                                     | Command                                       |
| ------------------------------------------ | --------------------------------------------- |
| Run all tests                              | `pytest`                                      |
| Run one test file                          | `pytest path-to-test_file\file.py`            |
| Run one test function                      | `pytest path-to-test_file\file.py::test_name` |
| Show detailed output                       | `pytest -v`                                   |
| Stop on first fail                         | `pytest -x`                                   |
| Print output in test functions (`print()`) | `pytest -s`                                   |

### How It Works

When Pytest runs:

- It scans for files matching `test_*.py` or `*_test.py`.
- Inside those files, it looks for functions starting with `test_`.
- Each test is run in isolation.
- If `assert` fails, `pytest` logs the failure, what was expected, and what it got.