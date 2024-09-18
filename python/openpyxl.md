# Openpyxl Python Module Notes

**Python library to read/write Excel 2010 xlsx/xlsm/xltx/xltm files**.

## Basics
### Loading And Reading Excel Files

```python
import openpyxl

# Load the workbook
spreadsheet = load_workbook("path-to-file.xlsx")

# Access the active sheet (by default the first sheet)
active_sheet = spreadsheet.active

# Access a specific sheet by name
sheet_by_name = spreadsheet['Sheet2']  # Replace 'Sheet2' with the actual sheet name

# Access a specific sheet by index
sheet_by_index = spreadsheet.worksheets[1]  # Index starts from 0, so this is the second sheet

```

### Looping Over Rows And Columns

```python
# Iterate over all rows in the active sheet
for row in active_sheet.iter_rows(values_only=True):
    print(row) # Prints each row as a tuple with cell values

# Iterate over all rows in a specific sheet (e.g., 'Sheet2')
for row in sheet_by_name.iter_rows(values_only=True):
    print(row) # Prints each row as a tuple with cell values


# Iterate over rows (starting from the second row)
for row in sheet.iter_rows(min_row=2, max_row=5, values_only=True):
    print(row)  # Access rows between 2 and 5

# Iterate over columns (from A to C)
for col in sheet.iter_cols(min_col=1, max_col=3, values_only=True):
    print(col)  # Access columns A to C

```

### Modifying Existing Excel Files

```python
from openpyxl import load_workbook

# Load the existing workbook
workbook = load_workbook('example.xlsx')
sheet = workbook.active  # Select the active sheet

# Modify an existing cell
sheet['A1'] = 'Updated Value'

# Save the workbook after modification
workbook.save('example_modified.xlsx')
```

### Writing To A New Excel File

```python
from openpyxl import Workbook

# Create a new Workbook
workbook = Workbook()
sheet = workbook.active  # Select the default active sheet

# Writing data to cells
sheet['A1'] = 'Hello'
sheet['B1'] = 'World'
sheet.append([1, 2, 3])  # Append a new row of data

# Save the workbook to a file
workbook.save('newfile.xlsx')
```

## Cookbook & Examples

### Looping Over All Sheets In A File

```python
from openpyxl import load_workbook

# Load the workbook
workbook = load_workbook('example.xlsx')

# Loop through all the sheets in the workbook
for sheet in workbook.worksheets:
    print(f"Sheet name: {sheet.title}")  # Print the sheet name

    # Loop through the rows in the current sheet
    for row in sheet.iter_rows(values_only=True):
        print(row)  # Print each row's values

    print("-" * 40)  # Separator between sheets

```


Output:

```python
Sheet name: Sheet1
('Header1', 'Header2', 'Header3')
('Row1_Val1', 'Row1_Val2', 'Row1_Val3')
('Row2_Val1', 'Row2_Val2', 'Row2_Val3')
----------------------------------------
Sheet name: Sheet2
('HeaderA', 'HeaderB', 'HeaderC')
('Row1_A', 'Row1_B', 'Row1_C')
('Row2_A', 'Row2_B', 'Row2_C')
----------------------------------------
```
#### Explanation:

- **`workbook.worksheets`**: This retrieves a list of all the sheets in the workbook.
- **`sheet.title`**: This gives you the name of the current sheet.
- **`sheet.iter_rows(values_only=True)`**: This iterates over all rows in the current sheet. The `values_only=True` flag ensures that only the values of the cells (not the cell objects themselves) are returned.
- Each sheet's rows are printed in sequence, followed by a separator line (`"-" * 40`).
## Accessing Values By Column Names

When working with `openpyxl` to access values by column names while iterating over rows, you'll need to retrieve the column headers (the first row in the spreadsheet) and then map these headers to their columns.

Here's how you can do this:

1. Read the column headers from the first row.
2. Create a mapping between column names and their indexes.
3. Use that mapping to access values in each row by their column names.

```python
from openpyxl import load_workbook

# Load the workbook and select the active sheet
workbook = load_workbook('your_file.xlsx')
sheet = workbook.active

# Get the headers from the first row
headers = {cell.value: cell.column for cell in sheet[1]}

# Iterate over the rows, starting from the second row (data starts from row 2)
for row in sheet.iter_rows(min_row=2, values_only=True):
    # Access values by column names using the header mapping
    column_value_a = row[headers['ColumnA'] - 1]  # Access value of 'ColumnA'
    column_value_b = row[headers['ColumnB'] - 1]  # Access value of 'ColumnB'
    
    # Process the data
    print(f"ColumnA: {column_value_a}, ColumnB: {column_value_b}")

```

`values_only=True` in `iter_rows()` ensures that only the cell values (not the cell objects) are returned.
