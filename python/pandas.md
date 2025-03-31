# Pandas Python Library Notes

"Basics" of this note has been copied from pandas-cheat-sheet note from this [repo]([GitHub - KeithGalli/complete-pandas-tutorial: A comprehensive tutorial on the Python Pandas library, updated to be consistent with best practices and features available in 2024.](https://github.com/KeithGalli/complete-pandas-tutorial)), credits go to [KeithGalli](https://github.com/KeithGalli). 
## Basics
### Creating DataFrames

Creating DataFrames is the foundation of using Pandas. Here’s how to create a simple DataFrame and display its content.

```
import pandas as pd
import numpy as np

# Create a simple DataFrame
df = pd.DataFrame(
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12]],
    columns=["A", "B", "C"],
    index=["x", "y", "z", "zz"]
)

# Display the first few rows
df.head()

# Display the last two rows
df.tail(2)
```

### Loading Data

Loading data into DataFrames from various file formats is crucial for real-world data analysis.

```
# Load data from CSV
coffee = pd.read_csv('./warmup-data/coffee.csv')

# Load data from Parquet
results = pd.read_parquet('./data/results.parquet')

# Load data from Excel
olympics_data = pd.read_excel('./data/olympics-data.xlsx', sheet_name="results")
```

### Accessing Data

Accessing different parts of the DataFrame allows for flexible data manipulation and inspection.

```
# Access columns
df.columns

# Access index
df.index.tolist()

# General info about the DataFrame
df.info()

# Statistical summary
df.describe()

# Number of unique values in each column
df.nunique()

# Access unique values in a column
df['A'].unique()

# Shape and size of DataFrame
df.shape
df.size
```

### Filtering Data

Filtering data is essential for extracting relevant subsets based on conditions.

```
# Filter rows based on conditions
bios.loc[bios["height_cm"] > 215]

# Multiple conditions
bios[(bios['height_cm'] > 215) & (bios['born_country']=='USA')]

# Filter by string conditions
bios[bios['name'].str.contains("keith", case=False)]

# Regex filters
bios[bios['name'].str.contains(r'^[AEIOUaeiou]', na=False)]
```

### Adding/Removing Columns

Adding and removing columns is important for maintaining and analyzing relevant data.

```
# Add a new column
coffee['price'] = 4.99

# Conditional column
coffee['new_price'] = np.where(coffee['Coffee Type']=='Espresso', 3.99, 5.99)

# Remove a column
coffee.drop(columns=['price'], inplace=True)

# Rename columns
coffee.rename(columns={'new_price': 'price'}, inplace=True)

# Create new columns from existing ones
coffee['revenue'] = coffee['Units Sold'] * coffee['price']
```

### Merging and Concatenating Data

Merging and concatenating DataFrames is useful for combining different datasets for comprehensive analysis.

```
# Merge DataFrames
nocs = pd.read_csv('./data/noc_regions.csv')
bios_new = pd.merge(bios, nocs, left_on='born_country', right_on='NOC', how='left')

# Concatenate DataFrames
usa = bios[bios['born_country']=='USA'].copy()
gbr = bios[bios['born_country']=='GBR'].copy()
new_df = pd.concat([usa, gbr])
```

### Handling Null Values

Handling null values is essential to ensure the integrity of data analysis.

```
# Fill NaNs with a specific value
coffee['Units Sold'].fillna(0, inplace=True)

# Interpolate missing values
coffee['Units Sold'].interpolate(inplace=True)

# Drop rows with NaNs
coffee.dropna(subset=['Units Sold'], inplace=True)
```

### Aggregating Data

Aggregation functions like value counts and group by help in summarizing data efficiently.

```
# Value counts
bios['born_city'].value_counts()

# Group by and aggregation
coffee.groupby(['Coffee Type'])['Units Sold'].sum()
coffee.groupby(['Coffee Type'])['Units Sold'].mean()

# Pivot table
pivot = coffee.pivot(columns='Coffee Type', index='Day', values='revenue')
```

### Advanced Functionality

Advanced functionalities such as rolling calculations, rankings, and shifts can provide deeper insights.

```
# Cumulative sum
coffee['cumsum'] = coffee['Units Sold'].cumsum()

# Rolling window
latte = coffee[coffee['Coffee Type']=="Latte"].copy()
latte['3day'] = latte['Units Sold'].rolling(3).sum()

# Rank
bios['height_rank'] = bios['height_cm'].rank(ascending=False)

# Shift
coffee['yesterday_revenue'] = coffee['revenue'].shift(1)
```

### New Functionality

The PyArrow backend offers optimized performance for certain operations, particularly string operations.

```
# PyArrow backend
results_arrow = pd.read_csv('./data/results.csv', engine='pyarrow', dtype_backend='pyarrow')
results_arrow.info()
```
## Examples & Cookbook
### Creating an In-Memory Excel File from a DataFrame

You can create a DataFrame and use the `BytesIO` object as a file-like buffer to save the Excel content.

### Example:

```python
import pandas as pd
from io import BytesIO

# Create a sample DataFrame
df = pd.DataFrame({
    'Name': ['Alice', 'Bob', 'Charlie'],
    'Age': [25, 30, 35],
    'City': ['New York', 'Los Angeles', 'Chicago']
})

# Create a BytesIO buffer to hold the Excel file in memory
excel_buffer = BytesIO()

# Write the DataFrame to this in-memory Excel file (buffer)
with pd.ExcelWriter(excel_buffer, engine='xlsxwriter') as writer:
    df.to_excel(writer, index=False)

# To simulate reading it back into a DataFrame (like for testing):
excel_buffer.seek(0)  # Rewind the buffer to the beginning
df_read = pd.read_excel(excel_buffer)

# Use pandas.testing to compare the original and the read DataFrame
pd.testing.assert_frame_equal(df, df_read)
```


### Explanation:

- **`BytesIO`**: Acts as an in-memory buffer that mimics file-like behavior, avoiding the need for actual disk IO.
- **`ExcelWriter`**: Writes the DataFrame to the Excel format using the `xlsxwriter` engine.
- **`excel_buffer.seek(0)`**: Resets the buffer pointer to the beginning so the data can be read back.
- **`pandas.testing.assert_frame_equal`**: A useful function for unit testing, allowing you to verify that the original and read DataFrames are equal.

You can now use this in-memory Excel file in your unit tests without creating any files on disk.


### Writing DataFrame.info() output to log

Since dataframe.info() does not return a string but writes directly to sys.stdout, output first needs to be captured using io.StringIO.

```python
import logging
import pandas as pd
import io

# Configure logging
logging.basicConfig(level=logging.INFO, format="%(asctime)s - %(levelname)s - %(message)s")
logger = logging.getLogger(__name__)

# Sample DataFrame
df = pd.DataFrame({'A': [1, 2, 3], 'B': [4.5, 5.5, 6.5], 'C': ["x", "y", "z"]})

# Capture df.info() output
buffer = io.StringIO()
# Redirect info output to buffer
df.info(buf=buffer)  
# Get string from buffer
info_str = buffer.getvalue()  

# Log the DataFrame info
logger.info("\n" + info_str)
```