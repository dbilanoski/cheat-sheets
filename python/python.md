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
python -m venv C:\path\to\your\project-root-folder

# Activate it in Windows
C:\path-to-your-project-folder\Scripts\activate.bat

# Activate it on Linux\Mac
source path-to-your-project-folder/bin/activate
```



## References
1. [Official Docs on Venv](https://docs.python.org/3/library/venv.html)