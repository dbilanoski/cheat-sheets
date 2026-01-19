# The Dream Code Strategy

## A Workflow for Clean Python Architecture

The most common issue in scripting is **Main Function Sprawl**: `main()` starts clean but quickly becomes a mess of low-level logic, nested loops, and error handling.

To prevent this, use the **Top-Down "Dream Code" Workflow**.

### Phase 1: The Comment Skeleton

**Do not write executable code yet.** Write the "story" of your script using comments inside `main()`. This forces you to plan the flow without getting bogged down in syntax.

```
def main():
    # 1. Define Defaults (Exit codes, flags)
    
    try:
        # 2. Load Configuration & Environment
        
        # 3. Connect to API/Database
        
        # 4. Find & Filter Files
        
        # 5. Loop through files
            # A. Read & Transform
            # B. Upload
            # C. Archive
            
    except Exception:
        # 6. Global Crash Handler
        pass
        
    finally:
        # 7. Cleanup & Logs
        pass
```

### Phase 2: The Dream Code (The Traffic Controller)

Write the code inside `main()` calling functions that **don't exist yet**. Write the code you _wish_ you had.

**Rule:** `main()` handles **WHAT** happens, not **HOW** it happens.

```
def main() -> int:
    exit_code = 0
    
    try:
        # High-level abstractions only
        config = load_app_config() 
        gc_api = initialize_api(config)
        files = get_source_files(config["source_dir"])

        for file in files:
            try:
                # Pass data to a worker function
                stats = process_single_file(file, config, gc_api)
                
                send_notification("SUCCESS", file.name, stats)
                
            except Exception as e:
                # Handle file-level errors here
                logging.error(f"File {file.name} failed: {e}")
                send_notification("ERROR", file.name, str(e))
                exit_code = 1 # Mark job as partial failure, but continue

    except Exception as e:
        # Handle critical app crashes here
        handle_critical_failure(e)
        exit_code = 1
        
    finally:
        perform_cleanup(config)
        
    return exit_code
```

### Phase 3: Stubbing

Create the function definitions to make the script valid (runnable), but don't write the logic yet. This creates your "To-Do List."

```
def load_app_config():
    # TODO: Implement .env loading and validation
    return {}

def process_single_file(file_path, config, api):
    # TODO: Implement Excel parsing, transformation, and API upload
    return "Processed 0 rows"

def perform_cleanup(config):
    # TODO: Delete old logs
    pass
```

### Phase 4: Implementation

Pick one function (e.g., `process_single_file`) and focus entirely on that.

- You don't need to worry about global error handling (handled in `main`).
- You don't need to worry about config loading (passed in arguments).
- **Tip:** If a function grows beyond 50 lines, break it down further (e.g., `read_excel`, `upload_batch`).
- You can actually **run** this script. It won't do anything, but it will run without crashing


### The "Smell Test" for Refactoring

**1. The "One Screen" Rule** If you have to scroll down to see the end of `main()`, it is too big. It should fit on one screen so you can see the high-level flow at a glance.

**2. The "Mixed Abstraction" Rule** `main()` should operate at a high level. If you see low-level logic, extract it.

- ✅ **Good:** `config = load_config()`
- ❌ **Bad:** `db_pass = os.getenv("DB_PASS")`
- ❌ **Bad:** `if filename.endswith('.xlsx'):`
- ❌ **Bad:** `df['col'] = df['col'].str.strip()`