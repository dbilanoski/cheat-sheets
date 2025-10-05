# Working with Databases Using pyodbc

`pyodbc` is a lightweight and reliable tool for connecting Python to SQL databases. It provides:

- A simple way to connect via ODBC drivers.
- Context-managed connections and cursors.
- Broad compatibility across SQL Server versions.
- Easy integration with logging and configuration management.


## Prerequisites

Before using `pyodbc`, make sure that:

- An appropriate **ODBC driver** is installed for your database.  
- For Microsoft SQL Server, common options include:
    - ODBC Driver 18 for SQL Server
    - ODBC Driver 17 for SQL Server
    - SQL Native Client 11.0
- Once `pyodbc` is installed, You can list the drivers available on your system with:
  
  ```python
	  import pyodbcv
	  print(pyodbc.drivers())
  ```

## Basic usage

To install the library:

```powershell
pip install pyodbc
```

Basic example demonstrating how to connect, execute a query, and read results.

```python
import pyodbc

# Define connection parameters
driver = "ODBC Driver 18 for SQL Server"
server = "localhost\\SQLEXPRESS"
database = "TestDB"
username = "my_username"
password = "my_password"

# Build the connection string for SQL authentication
# Each segment defines part of the connection
connection_string = (
    f"DRIVER={{{driver}}};"      # The ODBC driver to use
    f"SERVER={server};"          # Target SQL Server instance
    f"DATABASE={database};"      # Database to connect to
    f"UID={username};"           # Username for SQL login
    f"PWD={password}"            # Password for SQL login
)

# Attempt to connect
try:
    conn = pyodbc.connect(connection_string)
    print("Connection successful.")

    # Create a cursor to execute SQL statements
    cursor = conn.cursor()

    # Execute a simple SQL SELECT query
    cursor.execute("SELECT TOP 5 * FROM Employees")

    # Retrieve column names
    columns = [col[0] for col in cursor.description]
    print("Columns:", columns)

    # Fetch all rows and display them
    for row in cursor.fetchall():
        print(row)

    # Always close cursor and connection
    cursor.close()
    conn.close()

except pyodbc.Error as e:
    print("Database error occurred:", e)

```

### Using the Current Windows User Context

If your SQL Server uses **Windows Authentication**, you can connect using your current login session instead of username and password by configuring a `Trusted_Connection=yes`:

```python
connection_string = (
    f"DRIVER={{{driver}}};"
    f"SERVER={server};"
    f"DATABASE={database};"
    "Trusted_Connection=yes"
)

```

This authenticates using the Windows user who is running the Python process.
Useful for automation when task is scheduled in the context of the user having rights to access database.

## Real World Example

* It uses `config` dictionary to which data is stored from the environment.
* It features a `validate_odbc_driver` function which takes user specified driver and checks if it's present in the system. If not, it will try other fallback drivers until it finds suitable one.
* It features `get_data_from_database` function used to pass SQL query to it and retrieve data from the databse. 
  

```python
import pyodbc
import logging

config = {
    "sql": {
        "driver": "ODBC Driver 18 for SQL Server",
        "server": "localhost\\SQLEXPRESS",
        "database": "TestDB"
    }
}

def validate_odbc_driver(user_specified_driver: str) -> str | None:
    """
    Takes ODBC driver specified by the user and checks if it's installed,
    offering fallback options if it's missing.

    Params:
    - user_specified_driver (str): Driver name provided by the user.

    Returns:
    - (str | None): The driver name found on the system, or None if not found.
    """

    fallback_drivers = [
        'ODBC Driver 18 for SQL Server',
        'ODBC Driver 17 for SQL Server',
        'ODBC Driver 13.1 for SQL Server',
        'ODBC Driver 13 for SQL Server',
        'SQL Native Client 11.0'
    ]

    try:
        available_drivers = pyodbc.drivers()
    except Exception as e:
        logging.error(f"Failed to retrieve available ODBC drivers: {e}", exc_info=True)
        return None

    logging.debug(f"Available ODBC drivers: {available_drivers}")

    if user_specified_driver in available_drivers:
        logging.info(f"{user_specified_driver} driver confirmed on the system.")
        return user_specified_driver

    for driver in fallback_drivers:
        logging.info(f"{user_specified_driver} not found. Checking fallback drivers...")
        if driver in available_drivers:
            logging.info(f"{driver} driver confirmed on the system.")
            return driver

    logging.error(f"No valid ODBC driver found among: {fallback_drivers + [user_specified_driver]}")
    logging.error(f"System drivers: {available_drivers}")
    return None


def get_data_from_database(config: dict, query: str) -> dict | None:
    """
    Connects to the SQL Server using parameters in the config dictionary,
    executes a SQL query, and returns the result set.

    Params:
    - config (dict): Contains 'driver', 'server', and 'database' under config['sql'].
    - query (str): SQL query to execute.

    Returns:
    - dict | None: Dictionary with 'column_names' and 'data_rows', or None on failure.
    """

    try:
        connection_string = (
            f"DRIVER={config['sql']['driver']};"
            f"SERVER={config['sql']['server']};"
            f"DATABASE={config['sql']['database']};"
            r"Trusted_Connection=yes"
        )

        logging.debug(f"Constructed connection string: {connection_string}")

        with pyodbc.connect(connection_string) as conn:
            logging.info(f"Connected to {config['sql']['server']} successfully.")

            with conn.cursor() as cursor:
                logging.info("Executing query...")
                cursor.execute(query)

                if cursor.description:
                    column_names = [col[0] for col in cursor.description]
                    data_rows = cursor.fetchall()
                    logging.info(f"Query successful, retrieved {len(data_rows)} rows.")
                    return {"column_names": column_names, "data_rows": data_rows}

                logging.warning("Query returned no result set.")
                return None

    except pyodbc.Error as e:
        logging.error(f"Database operation failed: {e}", exc_info=True)
        return None

    except Exception as e:
        logging.error(f"Unexpected error: {e}", exc_info=True)
        return None
```


### Testing The Connection

Example below:
- Validates that a compatible ODBC driver exists.
- Attempts to connect using your configuration.
- Executes a simple system query (`sys.objects`) to verify that communication with SQL Server works.
- Prints results directly to the console.
  

```python

# Configure logging
logging.basicConfig(level=logging.INFO, format="%(asctime)s [%(levelname)s] %(message)s")

# Define configuration for the SQL Server connection
config = {
	"sql": {
		"driver": validate_odbc_driver("ODBC Driver 18 for SQL Server"),
		"server": "localhost\\SQLEXPRESS",
		"database": "TestDB"
	}
}

# Verify that a valid driver was found
if not config["sql"]["driver"]:
	logging.error("No valid ODBC driver found. Aborting test.")
else:
	# Test query
	test_query = "SELECT TOP 5 name, object_id FROM sys.objects"

	# Fetch data
	result = get_data_from_database(config, test_query)

	if result:
		print("\nConnection and query test successful.")
		print("Columns:", result["column_names"])
		for row in result["data_rows"]:
			print(row)
	else:
		print("\nTest failed or query returned no results.")
```


## Best Practices
- **Use context managers**  
    Always use `with` blocks for both connections and cursors to ensure proper cleanup.
    
- **Validate driver availability**  
    Confirm that the expected driver exists before connecting. Include fallback options for cross-machine compatibility.
    
- **Keep configuration external**  
    Store connection settings in configuration files or environment variables, not hardcoded in the script.
    
- **Handle credentials securely**  
    Prefer Windows Authentication (`Trusted_Connection=yes`) where possible. Never store plain-text passwords in code.
    
- **Fetch data efficiently**  
    Use `fetchmany(size)` or iterate through cursor results to avoid loading large datasets entirely into memory.
    
- **Close connections explicitly when not using context managers**  
    Always call `.close()` on cursors and connections if not handled automatically.