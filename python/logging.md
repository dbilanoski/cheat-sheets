# Python Logging Module Notes

Module which provide a standard error logging mechanism in Python.
It's included in Python, no need to install it.

### Basic Configuration
```python
import logging

# Basic configuration for logging
logging.basicConfig(
    filename='app.log',            # Name of the log file
    level=logging.DEBUG,           # Logging level (DEBUG, INFO, WARNING, ERROR, CRITICAL)
    format='%(asctime)s - %(levelname)s - %(message)s',  # Log format
    datefmt='%Y-%m-%d %H:%M:%S'    # Date format in the log file
)

# Example usage of different logging levels
logging.debug('This is a DEBUG message')
logging.info('This is an INFO message')
logging.warning('This is a WARNING message')
logging.error('This is an ERROR message')
logging.critical('This is a CRITICAL message')

```

### Configuration Meaning

- `filename='app.log'`: Specifies the name of the file where the logs will be saved.
- `level=logging.DEBUG`: Sets the minimum level of logging. Here it's set to `DEBUG`, so all messages of level `DEBUG` and higher will be logged (DEBUG, INFO, WARNING, ERROR, CRITICAL).
- `format='%(asctime)s - %(levelname)s - %(message)s'`: Defines the format of the log message. It includes the timestamp (`asctime`), the logging level (`levelname`), and the actual log message (`message`).
- `datefmt='%Y-%m-%d %H:%M:%S'`: Specifies the date format in the log file.

### Usage

- Log messages are saved to the file `app.log`.
- You can control the level of detail by changing the `level` parameter.
- You can log different levels of information using `logging.debug()`, `logging.info()`, `logging.warning()`, `logging.error()`, and `logging.critical()`.

### Example `app.log` Output
```text
2024-09-12 10:45:12 - DEBUG - This is a DEBUG message
2024-09-12 10:45:12 - INFO - This is an INFO message
2024-09-12 10:45:12 - WARNING - This is a WARNING message
2024-09-12 10:45:12 - ERROR - This is an ERROR message
2024-09-12 10:45:12 - CRITICAL - This is a CRITICAL message
```
