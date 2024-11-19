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
	#  * dotenv_path=dotenv_pat is only needed if .env file is not in a root directory
	load_dotenv(dotenv_path=dotenv_path)
	
	
	# Access the variables using os.getenv
	api_key = os.getenv("API_KEY")  # Retrieve the API_KEY
	database_url = os.getenv("DATABASE_URL")  # Retrieve the database URL
	debug_mode = os.getenv("DEBUG")  # Retrieve the debug mode
	
	# Example usage
	print(f"API Key: {api_key}")
	print(f"Database URL: {database_url}")
	print(f"Debug Mode: {debug_mode}")

	```

## Best Practices

1. Exclude the `.env` file from version control: Add `.env` to your `.gitignore` file to prevent sensitive information from being accidentally shared.
    
    `# .gitignore .env`
    
2. Use `.env.example` for reference. Create a `dotenv.example` file with placeholder values to share the required environment variables without exposing sensitive data:
   
    `API_KEY=your_api_key_here DATABASE_URL=your_database_url_here DEBUG=True`
    
3. Use `os.environ` for default or fallback values. When using `os.getenv`, provide a default value to handle cases where the `.env` file might be missing or incomplete:
    
    `api_key = os.getenv("API_KEY", "default_api_key")`

## References
1. [Official docs on dotenv library](https://pypi.org/project/python-dotenv/)