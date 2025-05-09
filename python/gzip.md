# Archiving With `gzip`

The `gzip` module in Python allows you to easily compress and decompress single files using the popular `.gz` format, making storage and transfer more efficient.


## Basic Usage

```python

import gzip
import shutil

def compress_file(source_path, destination_path):
    """
    Compress a file into .gz format.

    Params:
    - source_path (str): Path to the original file.
    - destination_path (str): Path to save the compressed .gz file.
    """
    # Open the source file in binary read mode
    with open(source_path, 'rb') as source_file:
        # Open the destination .gz file in binary write mode
        with gzip.open(destination_path, 'wb') as compressed_file:
            # Copy the contents of the source file into the compressed file
            shutil.copyfileobj(source_file, compressed_file)



# Example usage:
# compress_file('example.txt', 'example.txt.gz')


def decompress_file(source_path, destination_path):
    """
    Decompress a .gz file into a normal file.

    Params:
    - source_path (str): Path to the compressed .gz file.
    - destination_path (str): Path to save the decompressed file.
    """
    # Open the compressed .gz file in binary read mode
    with gzip.open(source_path, 'rb') as compressed_file:
        # Open the output file in binary write mode
        with open(destination_path, 'wb') as extracted_file:
            # Copy the contents of the compressed file into the output file
            shutil.copyfileobj(compressed_file, extracted_file)

# Example usage:
# decompress_file('example.txt.gz', 'example_extracted.txt')

```


### Handling Large Files

When working with very large `.gz` files (like logs or datasets), it is best to read the file **line-by-line** to avoid loading everything into memory at once.

```python
import gzip

def read_gzip_file_line_by_line(file_path):
    """
    Read a .gz file line-by-line without loading the entire file into memory.

    Params:
    - file_path (str): Path to the .gz compressed file.
    """
    # Open the compressed file in text read mode
    with gzip.open(file_path, 'rt') as file:
        # Loop through each line in the file
        for line in file:
            # Process each line (for now, just print it)
            print(line.strip())

# Example usage:
# read_gzip_file_line_by_line('example.txt.gz')


def write_lines_to_gzip(file_path, lines):
    """
    Write multiple lines into a .gz file, one line at a time.

    Params:
    - file_path (str): Path to the .gz compressed file to create.
    - lines (iterable): A list or generator of lines (strings) to write.
    """
    # Open the compressed file in text write mode
    with gzip.open(file_path, 'wt') as file:
        # Loop through each line in the given lines
        for line in lines:
            # Write each line followed by a newline character
            file.write(line + '\n')

# Example usage:
# Create a list of lines to write
sample_lines = [
    "This is the first line.",
    "This is the second line.",
    "This is the third line."
]

# Write these lines into a .gz file
# write_lines_to_gzip('sample_output.txt.gz', sample_lines)

```

## Best Practices

- **Use `with` statements**: Always manage file operations inside `with` blocks to ensure safe handling of files.
- **Handle files in binary mode**: Open files in `'rb'` and `'wb'` mode when compressing or decompressing to make sure process will work regardless of the file type (images, executables, etc.).
- **Use `shutil.copyfileobj()`**: It efficiently handles large files without loading everything into memory at once.
- **Name your files properly**: Use `.gz` extensions for compressed files to avoid confusion.
- **Remember `.gz` is for single files**: Use `.tar.gz` if you need to compress multiple files together.