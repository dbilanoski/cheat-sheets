# Python Logging Module Notes

Module which provide a standard error logging mechanism in Python.
It's included in Python, no need to install it.

## Basic Configuration
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

## Extended Configuration

Logger can be configured to have multiple handlers by separately instantiating root logger, then adding separately defined handlers to the root logger.

Example with both file and console handlers where the logging get outputted both to a file and a console.

```python

import logging

def configure_logging(main_log_path: Path | str, level: str = "INFO") -> None:
    """
    Configures the main logger.

    Params:
    - main_log_path: Path for the main log file (updates).
    - level: String to determine logging level (DEBUG, INFO, WARNING, ERROR, CRITICAL). Defaults to "INFO".

	Raises:
	- ValueError if level not in [DEBUG, INFO, WARNING, ERROR, CRITICAL]
    """

    # Validate level arg
    level = level.upper()
    
    valid_levels = ["DEBUG", "INFO", "WARNING", "ERROR", "CRITICAL"]
    
    if level not in valid_levels:
        raise ValueError(f"Level needs to be one of: {', '.join(valid_levels)}")
    
    # Create log directory if it doesn't exist
    Path(main_log_path).parent.mkdir(parents=True, exist_ok=True)

    
    # Configure the logging formatter which can later be passed to handlers
    log_formatter: logging.Formatter = logging.Formatter(
        fmt="%(asctime)s - %(levelname)s - %(message)s",
        datefmt="%m/%d/%Y %I:%M:%S %p",
    )

    # Instantiate root looger (main logging mechanism)
    root_logger = logging.getLogger()

    # Clear existing handlers (to ensure clean start)
    root_logger.handlers.clear()

  
    # Set level (here we use getattr(object, level_name) to retrieve numeric value for the level keyword which the setLevel() needs)
    root_logger.setLevel(getattr(logging, level))

    # Create log directory if it doesn't exist
    Path(main_log_path).parent.mkdir(parents=True, exist_ok=True)


    # Conifgure the file handler
    file_handler: logging.FileHandler = logging.FileHandler(main_log_path, mode="a", encoding="utf-8")

    # Apply format to the handler
    file_handler.setFormatter(log_formatter)

    # Add handler to the root logger
    root_logger.addHandler(file_handler)

  
    # Configure the console handler
    console_handler: logging.StreamHandler = logging.StreamHandler()

    # Apply format
    console_handler.setFormatter(log_formatter)
    
    # Add handler to hte root logger
    root_logger.addHandler(console_handler)
```