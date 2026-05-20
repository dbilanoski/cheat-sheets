# GoLang Basics

Go (or Golang) is a statically typed, compiled language designed by Google. It combines the **runtime speed** of C++ with the simplicity of Python. 

It is primarily used for cloud infrastructure, microservices and high-performance CLI tools .

## Installation & Setup

1. **Download:** Get the installer from [golang.org](https://go.dev/dl/).
2. **Install:** Run the installer. It automatically sets up your `GOROOT` and adds `/bin` to your system `PATH`.
3. **Verify:** Open a terminal and run: `go version`

If it doesn't return a version, restart your terminal or check your System Environment Variables.

### Adding To Environment Variables

In powershell terminal:

```powershell
$currentPath = [Environment]::GetEnvironmentVariable("Path", "User")
$goPath = "C:\Go\bin"

if ($currentPath -notlike "*$goPath*") {
    [Environment]::SetEnvironmentVariable("Path", $currentPath + ";" + $goPath, "User")
    Write-Host "Go bin added to User PATH successfully!" -ForegroundColor Green
} else {
    Write-Host "Go bin is already in your PATH." -ForegroundColor Yellow
}
```

Or manually:

1.  Press the **Windows Key** and type **"env"**.
2. Select **"Edit the system environment variables"**.
3. Click the **Environment Variables** button at the bottom right.
4. Look at the top section (**User variables for [YourName]**):
    
    - Find the variable named **Path** and click **Edit**.
    - Click **New** and paste the path to your Go bin folder. By default, this is:
        > `C:\Go\bin` (or wherever you installed it).
        
5. Click **OK** on all windows.
6. **Restart VS Code** (or any open terminals). Environment changes don't apply to windows that are already open.
### VS Code Setup

For the best experience, install the **Go Extension (by Google)**.

- **Important:** Once installed, open a `.go` file. VS Code will ask to install "Analysis Tools." Click **Install All**. This enables "IntelliSense" (auto-complete and auto-imports).

## Project Structure & Initialization

Go is "Module" based. Every project needs a `go.mod` file to manage dependencies.

### Common Structure

```plaintext
/my-project
    ├── go.mod          # Project "manifest" (like requirements.txt)
    ├── go.sum          # Checksum of dependencies (auto-generated)
    ├── main.go         # Entry point
    ├── helpers.go      # Other project files (same package)
    ├── .env            # Local configuration
    └── /Executable     # Target folder for built binaries
```

### Starting a New Project


```powershell
mkdir my-app && cd my-app
go mod init my-app-name    # Creates the go.mod file
```

## Working with Dependencies

Unlike Python's `pip install`, Go manages dependencies via the source code.

1. **Adding a library:** `go get github.com/joho/godotenv`
2. **Cleaning up:** Run the "Tidy" command frequently:
    
	```powershell
	go mod tidy
	```
    
This scans your code, downloads missing modules, and removes unused ones.

## Building & Running

- **Development:** Use `go run .` to compile and run the project temporarily.
    
- **Production Build:**
    
    ```powershell
    go build -ldflags="-s -w" -o Executable/my_app.exe .
    ```
    
    - `-s -w`: Shrinks the file by stripping debug data (Production Standard, can be omitted if not sure).
    - `-o`: Specifies the output path and name.
    - `.`: Tells Go to compile **all** files in the current folder.

## Hello, World Example

```go
package main

import (
	"fmt"
	"log"
	"os"
	"path/filepath"
)

func main() {
	// 1. Get the path of the current EXE
	exePath, _ := os.Executable()
	exeDir := filepath.Dir(exePath)

	// 2. Format a message
	userName := os.Getenv("USERNAME") // Gets Windows username
	if userName == "" {
		userName = "User"
	}

	// 3. Print to Console
	fmt.Printf("Hello %s, this EXE is running from: %s\n", userName, exeDir)

	// 4. Record to Log (Simulated)
	log.Println("Hello World process completed successfully.")
}
```

### How to run it:

1. Save the code as `hello.go`.
2. Open your terminal in that folder.
3. Run **`go run hello.go`** to see it instantly.
4. Run **`go build hello.go`** to create the `.exe`.

## Issues, Tips & Best Practices

- **Package Main:** Every file in your folder should start with `package main` if they are part of the same executable.
- **Implicit Linking:** In Go, you don't need to "import" `helpers.go` into `main.go`. If they are in the same folder and package, they "see" each other automatically.
- **Defers:** Use `defer` for cleanup (closing files, closing SFTP). It ensures the action happens when the function exits, even if it crashes.
- **Capitalization:** * `var Name` (Capitalized): Exported/Public (visible to other packages).
    - `var name` (Lowercase): Private (only visible inside current package).

### Common Hiccups & Variables

#### The Initialization Trap

Package-level variables (outside functions) are calculated **before** `main()` runs.

- **The Bug:** `var date = os.Getenv("VAR")` will be **empty** because `.env` hasn't been loaded yet.
- **The Fix:** Declare the variable globally, but assign its value **inside** `main()` after `godotenv.Load()`.

#### The Execution Context

- **`os.Getwd()`**: Returns where your terminal is "standing." (Unreliable for scheduled tasks).
- **`os.Executable()`**: Returns where the actual `.exe` lives. (Always use this for pathing to `.env` or `Logs`).

#### The Empty File Bug

Go's `csv` and `bufio` writers are **buffered**. If you don't call `.Flush()` before the program exits, the data stays in RAM and the file on disk remains empty (0 bytes).


```go
writer.Flush()
file.Close()
```
