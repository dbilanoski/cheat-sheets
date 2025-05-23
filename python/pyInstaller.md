# PyInstaller Python Library Notes

**PyInstaller** is a Python package used to bundle Python applications into standalone executables, which can then run on systems without needing a Python interpreter or any additional libraries installed.


## Installation

```powershell
pip install pyinstaller
```


## Basic Usage

To generate an executable from your Python script, go to your app folder and run following command in the terminal (adjust script name):

```powershell
pyinstaller main.py
```

This creates a bundle **dist/** directory containing the standalone executable along with all the dependencies and libraries.

**Example**
Let’s say you work on Windows machine with venv and want to create a single-file executable with no console window, a custom icon and include all installed packages in a venv environment:

```powershell
pyinstaller --onefile --noconsole --icon=myicon.ico --paths ".\Lib\site-packages" myscript.py
```

### Common PyInstaller Options

#### One-file Mode
By default, PyInstaller generates multiple files for the executable. If you want a single bundled executable, use the `--onefile` option: 

```powershell
pyinstaller --onefile myscript.py
```

This will create a single `.exe` file (on Windows) or an equivalent executable (on Linux/Mac) inside the **dist/** folder.

#### Windowed (No Console) Mode
If your application has a GUI and you don’t want a console window to appear, add the `--noconsole` (or `--windowed`) option:

```powershell
pyinstaller --onefile --noconsole myscript.py
```

#### Specify Icon For The Executable

```powershell
pyinstaller --onefile --icon=myicon.ico myscript.py
```

#### Using And Modifying Spec Files
When you run PyInstaller, it creates a `.spec` file that describes how to build the application. You can modify this file if you need more complex configurations (like adding data files, setting environment variables, etc.).

To rebuild the executable after modifying the `.spec` file, run:

```powershell
pyinstaller myscript.spec
```

#### Include Data Files

If your application needs extra data files (like images or configurations), you can include them using the `--add-data` option. The syntax depends on the operating system:

**Windows**
```powershell
pyinstaller --add-data "path/to/data;data" myscript.py
```

**Mac/Linux**
```bash
pyinstaller --add-data "path/to/data:data" myscript.py
```


#### Specify Path Where Imports Are

A path to search for imports.
* Multiple paths are allowed, separated by ":"
* Equivalent to supplying the `pathex` argument in the spec file.

Example where default imports folder in a venv is used:

```powershell
pyinstaller --paths  ".\Lib\site-packages" myscript.py
```

#### Cleaning Up
To clean up temporary files generated by PyInstaller, run:

```powershell
pyinstaller --clean
```


## References

1.[Official pyinstaller docs](https://pyinstaller.org/en/stable/usage.html)
