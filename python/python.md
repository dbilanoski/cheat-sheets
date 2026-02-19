# Python Notes

## Modules & Packaging

Modules are reusable Python scripts and when working with multiple of them, it's called a package.

For a package to be functional in separate folders (those inside of the script root folder), each folder containing modules need to have a **`__init__.py`**.

```text
my-app/
	├─ modules/
	│  ├─ __init__.py
	   ├─ module_script.py
	   ├─ module_script2.py
	   ├─ modules_sub_folder/
		   ├─ __init__.py
		   ├─ module2_script.py
	├─ main.py
```

To use those in a **`main.py`** script:

```python
from modules import module_script
from modules.modules_sub_folder import module2_script

# Your code ... 
```

## Virtual Environments

```powershell
# Create the virtual environment
python -m venv C:\path\to\your\project-root-folder\venv

# Activate it in Windows
C:\path-to-your-project-folder\Scripts\activate.bat

# Activate it on Linux\Mac
source path-to-your-project-folder/bin/activate
```


## Generators & Yield

When you call a function that has `yield` in it, **none of the code inside that function actually runs yet**.

Assigning it to a variable simply creates the "generator object" (the paused conveyor belt). It sits there, taking up almost zero memory, patiently waiting for you to press the "start" button.

### The Two Phases of a Generator

#### Phase 1: Creating the Generator (No execution)

Python

```
# The code inside stream_files hasn't started. No network calls are made.
my_generator = api.stream_files('folder', '.csv') 

print(my_generator) 
# Output: <generator object GCPStorageAPI.stream_files at 0x000...>
```

#### Phase 2: Consuming the Generator (Execution begins)

The execution only starts when you ask it for an item. You can do this by:

- Using a `for` loop.
- Casting it to a `list()`.
- Using list comprehension: `[name for name, _ in my_generator]`
- Calling `next(my_generator)`.

### ⚠️ Generators Exhaust!

There is one extremely important catch you must remember now that you are saving them to variables: **Generators are single-use.** Once you iterate through it, the conveyor belt reaches the end and stops forever. You cannot loop through the exact same variable twice.

Python

```
my_generator = api.stream_files('folder', '.csv')

# First time: Works perfectly! Downloads and processes the files.
for file_name, file_buffer in my_generator:
    print(f"Got {file_name}")

# Second time: Does absolutely nothing. The generator is empty/exhausted.
for file_name, file_buffer in my_generator:
    print(f"Got {file_name}") # <--- This will never print
```

If you need to process the same stream of files twice, you either have to cast it to a list first (which puts everything in RAM) OR just call the method again to create a brand new generator (`my_new_gen = api.stream_files(...)`).

## References
1. [Official Docs on Venv](https://docs.python.org/3/library/venv.html)