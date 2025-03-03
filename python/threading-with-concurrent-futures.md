# Threading in Python with concurrent.futures

The `concurrent.futures` module provides an easy way to run tasks concurrently using **thread pools**. It simplifies thread management by handling worker threads and scheduling tasks automatically. 

This is ideal for **I/O-bound operations** like API calls, file I/O, or database queries, where waiting times can be overlapped to improve performance.

## Basic Usage

In this example we'll be executing a function `your_task` which simulates a standard function you might want to run in parallel.

Steps:
- We define a `your_task` function that simulates an **I/O-bound** task (e.g. waiting for an API response).
- We instantiate a `ThreadPoolExecutor` with **3 worker threads** (`max_workers=3`).
- We submit **5 tasks** to the executor with the for loop, which runs them in parallel.
- `executor.submit(worker, i)` schedules tasks for execution and returns a `Future` object, which represents the pending result.
- `concurrent.futures.as_completed(futures)` allows us to process results **as soon as each task is completed**, rather than waiting for all tasks to finish.

```python
import concurrent.futures
import time

def your_task(task_id):
    """
    Simulates an I/O-bound task by sleeping for 2 seconds
    """
    
    print(f"Task {task_id} started")
    time.sleep(2)
    print(f"Task {task_id} finished")
    return taskid

# Create a ThreadPoolExecutor with 3 worker threads
with concurrent.futures.ThreadPoolExecutor(max_workers=3) as executor:
    # Initiate task list for the pool executor
    futures = []
    
    # Submit tasks to the executor
    for i in range(5):
	    # your_task function here is written without parenthesis, if there are arguments, they are added as beloq
	    # executor.submit(function_name, argument 1, argument 2, etc.)
        future = executor.submit(your_task, i)
        futures.append(future)
    
    # Wait for all tasks to complete before moving on
    concurrent.futures.wait(futures) 
```


## Safely Persisting Data with Thread Locks

Since threads share memory, concurrent writes to shared data structures can cause **race conditions** and **corrupt data**. The `threading.Lock` mechanism makes sure only one thread modifies shared data at a time.

Steps:
* We instantiate tracker variables in the global space
* We instantiate `threading.Lock()` to be used later as a context to lock the access only for the current thread.
* We declare `global` for the tracker variables we want to use
* Then we use `with lock` to safely write to counter variables from withing the thread.

```python
import concurrent.futures
import threading

# Instantiate trackers (shared resource)
counter = 0 
# Instantiate thread lock (later used as a context to lock the variable access to single thread at the time)
lock = threading.Lock()  

def safe_increment():
    """Increments counter safely using a lock"""
    # Declare usage of globally scoped trackers
    global counter
    # Make sure only one thread modifies `counter` at a time
    with lock: 
        temp = counter
        temp += 1
        counter = temp
    return counter

# Let's track executed function results here
results = []
# Create a ThreadPoolExecutor with 5 worker threads
with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
	# Submit tasks to the executor
	futures = []
	for _ in range(10):
		# safe_increment function does not have arguments
		future = executor.submit(safe_increment)
		futures.append(future)
	
	# Collect results as tasks complete (extra stuff, not needed for the thread lock)
	for future in concurrent.futures.as_completed(futures):
		results.append(future.result())

# Print
print("Final results:", results)
```


## Performance Considerations & System Resources

- Best for **I/O-bound tasks**. For CPU-heavy tasks, use `ProcessPoolExecutor` instead.
- The ideal number of threads depends on the workload but is typically **higher for I/O-bound tasks**.
- Check available CPU cores with:

	```python
	import os
	print(os.cpu_count())  

```
- Too many threads can cause excessive context switching, slowing down performance.


## Best Practices

* Prefer `ThreadPoolExecutor` over manually creating threads.  
* Choose `max_workers` carefully based on workload.  
* Use `Lock()` for shared resources to avoid race conditions.  
* Always handle exceptions properly in concurrent tasks.