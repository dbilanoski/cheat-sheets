# Argparse Library Notes

Built-it parser for command-line options, arguments and sub-commands.
* It defines what arguments the program requires, and [`argparse`](https://docs.python.org/3/library/argparse.html#module-argparse "argparse: Command-line option and argument parsing library.") will figure out how to parse those out of [`sys.argv`](https://docs.python.org/3/library/sys.html#sys.argv "sys.argv"). 
* The [`argparse`](https://docs.python.org/3/library/argparse.html#module-argparse "argparse: Command-line option and argument parsing library.") module also automatically generates help and usage messages. 
* The module will also issue errors when users give the program invalid arguments.


## Example

```python
import argparse

# Create argument parser
parser = argparse.ArgumentParser(description="A file processor script.")

# Add arguments
parser.add_argument("filename", help="The file to process.")  # Positional argument
parser.add_argument("-v", "--verbose", action="store_true", help="Enable verbose output.")  # Optional flag
parser.add_argument("--count", type=int, default=1, help="Number of times to process the file.")  # Optional argument with default

# Parse arguments
args = parser.parse_args()

# Use the arguments
if args.verbose:
    print(f"Verbose mode enabled.")
    
print(f"Processing file: {args.filename}")
print(f"Processing {args.count} time(s).")

```

To execute the script from command line:
`python myscript.py example.txt --verbose --count 3`

Output:
```text
Verbose mode enabled. 
Processing file: example.txt 
Processing 3 time(s).
```
## Basic Usage

### 1. Instantiate The "Parser"
Instantiate and configure the parser, which will act as a container for argument specification.

```python
parser = ArgumentParser(
	prog='ProgramName',
    description='What the program does',
    epilog='Text at the bottom of help')
```

### 2. Configure Argument Definitions
To add argument definitions, we use [`add_argument()`](https://docs.python.org/3/library/argparse.html#argparse.ArgumentParser.add_argument "argparse.ArgumentParser.add_argument") method. 
* These tell the [`ArgumentParser`](https://docs.python.org/3/library/argparse.html#argparse.ArgumentParser "argparse.ArgumentParser") how to take the strings on the command line and turn them into objects. 
* This information is stored and used when [`parse_args()`](https://docs.python.org/3/library/argparse.html#argparse.ArgumentParser.parse_args "argparse.ArgumentParser.parse_args") is called.

```python
# Positional argument
parser.add_argument("filename", 
					help="The file to process.")
# Optional flag 
parser.add_argument("-v", "--verbose",
					action="store_true", 
					help="Enable verbose output.") 
# Optional argument with default
parser.add_argument("--count", 
					type=int, 
					default=1, 
					help="Number of times to process the file.") 
```

### 3. Parse Defined Arguments
```python
args = parser.parse_args()
```


### Accessing Argument Values

Once the arguments are parsed, you can access them via the `args` object:
```python
if args.verbose: 
	print("Verbose mode is on.") 

print(f"Processing file: {args.filename}")
```

### Optional Arguments & Short Options

Argument can be defined to have short and long versions.
* Key is to set `action=store_true` so it can be tested with an if statement.


```python
parser.add_argument('-v', '--verbose', 
					help='Provides a verbose description.', 
					action='store_true') # This will store "True" if argument is used

# Parse the arguments
args = parser.parse_args()

# Differentiate between v and verbose
if args.verbose:
	print("This is a verbose description.")
else:
	print("This is standard description.)
```

### Hardcoding Choices


```python
parser.add_argument('-v', '--verbosity', 
					help='Change verbosity level.', 
					type='int',
					choices=[0, 1, 2]), # This will limit choices.
					default=0 # Configures a default in case value is not provided
					
# Parse the arguments
args = parser.parse_args()

# Differentiate between v and verbose
if args.verbosity == 2:
	print("This is a super verbose description.")
elif args.verbosity == 1:
	print("This is semi verbose descritpion.")
else:
	print("This is standard description.)
```

Similar can be achieved by counting how many times user wrote same argument.
* By adding the `action=count` it will count how many times -v was used.
* Example of usage `script.py -vvv` for three verbose arguments.

```python
parser.add_argument('-v', '--verbosity', 
					help='Change verbosity level.', 
					type='int',
					default=0, # Configures a default in case value is not provided
					action='count'
					
# Parse the arguments
args = parser.parse_args()

# Differentiate between v and verbose
if args.verbosity == 2:
	print("This is a super verbose description.")
elif args.verbosity == 1:
	print("This is semi verbose descritpion.")
else:
	print("This is standard description.)
```


## Examples

```python
import argparse

# This initializes the argument parser and provides a description for the script.
parser = argparse.ArgumentParser(description="A script that demonstrates argparse functionality.")

# Adding Positional Arguments
# Positional arguments are mandatory and must be provided in the specified order.
parser.add_argument("filename", help="The file to be processed.")

# Adding Optional Arguments (Flags)
# This optional argument is a flag. When used, it will set `verbose` to True (default is False).
parser.add_argument("-v", "--verbose", 
					action="store_true", 
					help="Enable verbose output.")

# Adding Optional Arguments with Values
# This optional argument expects an integer value. If not provided, it defaults to 1.
parser.add_argument("--count", 
					type=int, 
					default=1, 
					help="Number of times to repeat the operation (default is 1).")

# Adding Required Optional Arguments
# This optional argument must be provided by the user. It is required.
parser.add_argument("--output", 
					required=True, 
					help="The output file where results will be written.")

# Adding Arguments with Default Values
# If the user does not provide this argument, it defaults to "INFO".
parser.add_argument("--log-level", 
					choices=["DEBUG", "INFO", "WARNING", "ERROR"], 
					default="INFO", 
					help="Set the logging level. Options are: DEBUG, INFO, WARNING, ERROR (default is INFO).")

# Adding a Type Conversion Argument
# This argument expects an integer, and argparse will automatically convert the input.
parser.add_argument("--number", 
					type=int, 
					help="An integer value to demonstrate type conversion.")

# Parsing the Arguments
# This step processes the arguments provided by the user via the command line.
args = parser.parse_args()

# Accessing Argument Values
# You can now access the values of the arguments using the `args` object.
# Example: print the filename (positional argument).
print(f"Filename: {args.filename}")

# Using an Optional Flag
# If `--verbose` is provided, print an additional message.
if args.verbose:
    print("Verbose mode enabled. Extra output will be shown.")

# Using Optional Arguments with Default Values
# The count defaults to 1 but can be customized by the user with the `--count` argument.
for i in range(args.count):
    print(f"Processing {args.filename}... (Run {i+1} of {args.count})")

# Using Required Optional Arguments
# Since `--output` is required, the script will fail if the user doesn't provide it.
print(f"Results will be written to: {args.output}")

# Using a Default Value
# The `--log-level` argument defaults to "INFO" unless explicitly provided.
print(f"Log level set to: {args.log_level}")

# Handling Type Conversion
# The `--number` argument must be an integer. If provided, it is used here.
if args.number is not None:
    print(f"Provided number: {args.number} (type: {type(args.number)})")
else:
    print("No number provided.")

# Final message
print("Script execution complete!")

```

**Usage**
`python script.py file.txt --verbose --count 2 --output results.txt --log-level DEBUG --number 42
`

**Output**

```text
Filename: file.txt
Verbose mode enabled. Extra output will be shown.
Processing file.txt... (Run 1 of 2)
Processing file.txt... (Run 2 of 2)
Results will be written to: results.txt
Log level set to: DEBUG
Provided number: 42 (type: <class 'int'>)
Script execution complete!
```