# Python's Pathlib Module Notes

Object oriented take on working with filesystem. 
Benefits over other approaches include:
* Cross-platform compatibility - the `/` operator in `pathlib` works seamlessly across different operating systems.
* Simpler path manipulation - Uses the `/` operator for joining paths instead of `os.path.join()`.
* Combines path, file, and directory operations in one place.
* Readable path properties: Offers easy access to path components like `.name`, `.suffix`, `.parent`
* *Integrated pattern matching: Provides convenient globbing and recursive search with `.glob()` and `.rglob()`.

## Basic Usage

### Creating Path Object

```python
# Importing pathlib
from pathlib import Path

# Creating a Path Object
file_path = Path('example.txt') # A relative path 
directory_path = Path('/home/user/documents') # An absolute path

# Examples
root_dir = Path(".") # Returns scirpts root folder regardless of the OS
root_dir2 = Path(r".\data_import") # Returns scripts root folder\data import
root_dir2 = Path(r"C:\Users\Dan\Downloads") # Returns "C:\Users\Dan\Downloads"
```

### Path Operations

```python
# Use `/` to join paths (cross-platform) 
new_file = directory_path / 'new_file.txt' # Outputs: /home/user/documents/new_file.txt

# Get the absolute path of a file 
print(file_path.resolve()) #Outputs: /home/user/documents/example.txt

# Get the current working directory 
current_dir = Path.cwd()

# Get the user's home directory (e.g., /home/user or C:/Users/user) 
home_dir = Path.home()
```

#### Expanding Environment Variables Manually

```python
# Example: $HOME on Linux or %USERPROFILE% on Windows 
env_path = Path('$HOME/Documents').expanduser() 
```

### Path Check & File Properties

```python
# Check if it's file
file_path.is_file() # Returns True

# Check if it's directory
directory_path.is_dir() # Returns True

# Check if path exists
file_path.exists() # Returns True

# Returns parts of the paths
print(file_path.parts) # Returns ("home","user","documents","new_file.txt")

# Get the name of the file 
print(file_path.name) # Outputs: new_file.txt

# Get the file extension 
print(file_path.suffix) # Outputs: .txt 

# Get the parent directory 
print(file_path.parent) # Outputs: /home/user/documents
```

### File Metadata

```python
# Get file size in bytes
print(file_path.stat().st_size)

# Get file modification time
print(file_path.stat().st_mtime)

# Get file creation time
print(file_path.stat().st_ctime)
```

**Available attributes:**

- **st_mode**: It represents file type and file mode bits (permissions).
- **st_ino**: It represents the inode number on Unix and the file index on Windows platform.
- **st_dev**: It represents the identifier of the device on which this file resides.
- **st_nlink**: It represents the number of hard links.
- **st_uid**: It represents the user identifier of the file owner.
- **st_gid**: It represents the group identifier of the file owner.
- **st_size**: It represents the size of the file in bytes.
- **st_atime**: It represents the time of most recent access. It is expressed in seconds.
- **st_mtime**: It represents the time of most recent content modification. It is expressed in seconds.
- **st_ctime**: It represents the time of most recent metadata change on Unix and creation time on Windows. It is expressed in seconds.
- **st_atime_ns**: It is same as **st_atime** but the time is expressed in nanoseconds as an integer.
- **st_mtime_ns**: It is same as **st_mtime** but the time is expressed in nanoseconds as an integer.
- **st_ctime_ns**: It is same as **st_ctime** but the time is expressed in nanoseconds as an integer.
- **st_blocks**: It represents the number of 512-byte blocks allocated for file.
- **st_rdev**: It represents the type of device, if an inode device.
- **st_flags**: It represents the user defined flags for file.

### CRUD Operations

```python

# Create a new directory (if it doesn't exist) 
directory_path.mkdir(exist_ok=True) 

# Create nested directories (like `mkdir -p` in bash) 
nested_dir = Path('parent_dir/child_dir') 
nested_dir.mkdir(parents=True, exist_ok=True) 

# Write text to a file (creates file if it doesn't exist)
file_path.write_text("Hello, World!") # Overwrites content if file exists 

# Read the contents of a file 
file_content = file_path.read_text() # Reads the entire content as a string
print(file_content) # Outputs: Hello, World! 

# Rename (or move) a file 
renamed_file = file_path.rename('renamed_example.txt') # Moves/renames 'example.txt' 
# The target path may be absolute or relative. Relative paths are interpreted relative to the current working directory, _not_ the directory of the `Path` object.

# Move a file to a new directory 
moved_file = renamed_file.replace(directory_path / 'renamed_example.txt') # Moves it to documents 

# Delete a file 
if moved_file.exists(): 
	moved_file.unlink() # Removes the file 
	
# Delete an empty directory 
if directory_path.exists(): 
	directory_path.rmdir() # Only works if directory is empty 
	
# Remove a non-empty directory (use shutil for recursive deletion) 
if nested_dir.exists(): 
	# shutil needs to be imported prior for this to work
	shutil.rmtree(nested_dir) # Deletes the directory and all contents inside
```

### Pattern Matching (Glob)

```python

# Find all `.txt` files in a directory (non-recursive) 
for txt_file in directory_path.glob('*.txt'): 
	print(txt_file) # Lists .txt files in the specified directory 
	
# Recursive search for all `.py` files in subdirectories 
for py_file in directory_path.rglob('*.py'): 
	print(py_file) # Finds all Python files within this directory and subdirectories

# Better approach - store results to a list
files = list(directory_path.glob("*py")) # List containing all found py files
```

## References
1. [Pathlib benchmarks on switowski.com](https://switowski.com/blog/pathlib/)