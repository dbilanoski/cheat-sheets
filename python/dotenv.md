# dotenv Library Notes


The `python-dotenv` library simplifies the management of environment variables in Python applications. By using a `.env` file,  configuration data such as API keys, database credentials, or other environment-specific settings can be stored in a safe and organized manner. 

## Usage

1. Install the library
   ```powershell
   pip install python-dotenv

	```

2. Create a `.env` file in the root directory of your project and add your environment variables:
   
	```makefile
   API_KEY=your_api_key_here
   DATABASE_URL=postgres://username:password@localhost/dbname 
   DEBUG=True
	```

3. Load and access the variables in your Python code:

	```python
   
	from dotenv import load_dotenv
	import os
	
	# Load environment variables from the .env file
	load_dotenv()
	
	
	# Access the variables using os.getenv
	api_key = os.getenv("API_KEY")  # Retrieve the API_KEY
	database_url = os.getenv("DATABASE_URL")  # Retrieve the database URL
	debug_mode = os.getenv("DEBUG")  # Retrieve the debug mode
	
	# Example usage
	print(f"API Key: {api_key}")
	print(f"Database URL: {database_url}")
	print(f"Debug Mode: {debug_mode}")

	```


### Optional Parameters

Couple of parameters to override the default behaviours:

* **`dotenv_path (str | pathlib.Path)`** 
	* None by default where it will look for an .env in the current directory. 
	* Can take relative or absolute path to .env file. Useful when working with packaged executables and task schedulers where working directory might be different than the root directory of the app.
* **`stream (str | IO[str])`** 
	* A text stream that can be used if `dotenv_path` is None, defaults to None.
	* Works with `StringIO` allowing to load variables from other sources than a file
		*  `load_dotenv(stream=StringIO("USER=foo\nEMAIL=foo@example.org"))`
*  **`verbose (bool)`** 
	* If true, it will output warning that the .env file is missing. Defaults to False.
*  **`overide (bool)`** 
	* If true, it will overwrite the existing variables in the environment. Defaults to False.
*  **`encoding (str)`** 
	* Used to specify the encoding, defaults to utf-8.

### Real World Example

A snippet from a script which will be compiled to an .exe and executed via Windows task scheduler where:
* Root directory is first identified to ensure correct loading.
* Parameters are explicitly set for better readability.
  
```python
from dotenv import load_dotenv
from pathlib import Path
import os

# Determine the root directory
root_dir = None

if getattr(sys, "frozen", False):
	# If frozen attribute is found and set to false, means pyinstaller was used to package the executable
	# In this case, take the executables parent folder as a root dir
	# This ensures root dir is always relative to the executable, regardless of the execution context
	root_dir = Path(sys.executable).parent
else:
	# Otherwise, means we are running the py script so root can be it's parent folder
	root_dir = Path(__file__).parent

# Load the variables from the .env file in the root direcotry
load_dotenv(
	dotenv_path = Path(root_dir, ".env"), # Specify path to the .env
	verbose=False, # Disable verbosity
	override=False, # Disable overriding existing variables
	encoding="utf-8" # Specify utf-8 as encoding
)

```

## Best Practices

1. Exclude the `.env` file from version control: Add `.env` to your `.gitignore` file to prevent sensitive information from being accidentally shared.
    
    `# .gitignore .env`
    
2. Use `.env.example` for reference. Create a `dotenv.example` file with placeholder values to share the required environment variables without exposing sensitive data:
   
    `API_KEY=your_api_key_here DATABASE_URL=your_database_url_here DEBUG=True`
    
3. When using `os.getenv`, provide a default value to handle cases where the `.env` file might be missing or incomplete:
    
    `api_key = os.getenv("API_KEY", "default_api_key")`

## References
1. [Official docs on dotenv library](https://pypi.org/project/python-dotenv/)
