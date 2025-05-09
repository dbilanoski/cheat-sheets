# Parallelism in Python with concurrent.futures

The `concurrent.futures` module provides an easy way to run tasks concurrently using both thread pools and process pools. This simplifies the management of concurrent execution by handling worker processes or threads and scheduling tasks automatically.
## Parallelism with ThreadPoolExecutor

ThreadPoolExecutor uses multiple threads within a single Python process to achieve concurrency. This is well-suited for **I/O-bound operations** where the program spends a significant amount of time waiting for external resources. Idea is to overlap the waiting periods, reducing execution time.

**What it does:**

- Creates a pool of worker threads.
- Distributes submitted tasks across the available threads.
- Manages the execution and completion of these tasks.

**When to use it:**

- Downloading multiple files from the internet.
- Making multiple API calls.
- Reading data from multiple files on disk.
- Handling multiple client connections in a network server.


**Steps and example:**

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


## Parallelism with ProcessPoolExecutor

ProcessPoolExecutor creates multiple separate Python processes to achieve true parallelism. Each process has its own Python interpreter and memory space, allowing for the distribution of CPU-bound tasks across multiple processor cores, bypassing the Global Interpreter Lock (GIL) limitations in Python.

**What it does:**

- Creates a pool of worker processes.
- Distributes submitted tasks across the available processes using inter-process communication (IPC).
- Manages the execution and completion of these tasks in separate processes.

**When to use it:**

- Image and video processing - tasks like resizing images, applying filters, or encoding videos are often CPU-intensive and can be parallelized across multiple processes.
- Scientific computations and numerical analysis.
- Machine learning model training.
- Data compression and decompression.

**Steps and example**:

- We define a `calculate_sum` function that simulates an CPU intensive task.
- We instantiate a `ProcessPoolExecutor` with **3 workers** (`max_workers=3`).
- We iterate over a list of numbers and submit each with a calculate_sum function as a task to the executor, which runs them in parallel as separate processes.
- `executor.submit(worker, args)` schedules tasks for execution and returns a `Future` object, which represents the pending result.
- `concurrent.futures.as_completed(futures)` allows us to process results **as soon as each task is completed**, rather than waiting for all tasks to finish.

```python
import concurrent.futures
import time
import os

def calculate_sum(n):
    """
    Simulates a CPU-bound task by calculating the sum of numbers up to n.
    """
    
    print(f"Calculating sum up to {n} in process {os.getpid()}") 
    
    result = sum(range(n + 1)) print(f"Finished calculating sum up to {n}")
    return result
     
# Create a ThreadPoolExecutor with 3 worker threads
with concurrent.futures.ProcessPoolExecutor(max_workers=3) as executor:
    # Initiate task list for the pool executor
    futures = []
	# List of number for the sum function
	numbers = [10**7, 10**7 + 1000, 10**7 + 2000]
    
    # Iterate over numbers and submit each in a task to the executor
    for number in numbers:
	    # your_task function here is written without parenthesis, if there are arguments, they are added as beloq
	    # executor.submit(function_name, argument 1, argument 2, etc.)
        future = executor.submit(calculate_sum, number)
        futures.append(future)
    
    # Collect results as tasks complete (extra stuff, not needed for the thread lock)
    for future in concurrent.futures.as_completed(futures): 
    try: 
	    result = future.result() 
	    print(f"Sum calculated: {result}") 
	except Exception as e: 
		print(f"Calculation failed: {e}")
```
## Safely Persisting Data

### ThreadPoolExecutor and Thread Locks

Since threads share memory, concurrent writes to shared data structures can cause **race conditions** and **corrupt data**. The `threading.Lock` mechanism makes sure only one thread modifies shared data at a time.

Steps:
* We instantiate tracker variables in the global space
* We instantiate `threading.Lock()` to be used later as a context to lock the access only for the current thread.
* We declare `global` for the tracker variables we want to use
* Then we use `with lock` context statement to safely write to counter variables from within the thread. The lock is automatically released when the `with` block exits, even if exceptions occur.

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
	
	# Initiate task list for the pool executor
	futures = []
	# Iterate to produce multiple tasks and submit each to the executor
	for _ in range(10):
		# safe_increment function does not have arguments
		future = executor.submit(safe_increment)
		futures.append(future)
	
	# Wait for all tasks to complete before moving on
	concurent.futures.wait(futures)

# Print
print("Final count:", counter)
```

### ProcessPoolExecutor and Inter-Process Communication (IPC)

Processes in `ProcessPoolExecutor` have separate memory spaces,so direct sharing of variables and the `threading.Lock` mechanism will not work. To safely persist or share data between processes, Inter-Process Communication (IPC) mechanisms provided by the `multiprocessing` module are needed.

**Common IPC Mechanisms:**

- `multiprocessing.Queue` -  provides a thread-safe and process-safe way to pass messages and data between processes.
- `multiprocessing.Pipe` - creates a unidirectional or bidirectional communication channel between two processes.
- `multiprocessing.Value` and `multiprocessing.Array` -  allow creating shared memory objects that can be accessed by multiple processes. These often require explicit locking using `multiprocessing.Lock` to prevent race conditions at the process level.

**Example using `multiprocessing.Queue` to collect results:**

```python
import concurrent.futures
import multiprocessing
import time

def worker_task(task_id, data_queue):
	"""
	Defines the work to be done by each process in the pool. 
	It takes two arguments: 
	- task_id: A unique identifier for the task being processed. 
	- data_queue: A multiprocessing.Queue object used to send results back to the main process.
	"""
	# Create a result string that includes the process ID and the task ID. 
	result = f"Result from process {multiprocessing.current_process().pid} for task {task_id}" 
	# Simulate some work being done by the process (pauses execution for 0.5 seconds). 
	time.sleep(0.5) 
	# Put the generated 'result' string into the 'data_queue'. 
		# This is how the worker process sends data back to the main process.
	data_queue.put(result) 
	
	# Print a message indicating that the process has finished its task and put the result in the queue. 
	print(f"Process {multiprocessing.current_process().pid} finished task {task_id} and put result in queue")


# Instantiate a multiprocessing.Queue object. 
	# This queue will be used to receive results
result_queue = multiprocessing.Queue()

# Create a ProcessPoolExecutor with 5 worker processes
with concurrent.futures.ProcessPoolExecutor(max_workers=5) as executor:
    # Initiate task list for the pool executor
    futures = []
    # Iterate to produce multiple task and submit each to the executor
    for task in range(5):
	    # We're submiting our function worker_task, number from the loop as an task_id and result_queue as a data_queue to store results    
	    future = executor.submit(worker_task, task, result_queue)
	    
	# Wait for all submitted tasks to complete. 
		# This blocks the main process until all worker processes are done with tasks.
    concurrent.futures.wait(futures)

print("Collecting results from the queue:")

# Once all tasks are complete, print a message indicating the collection of results.
while not result_queue.empty():
    print(result_queue.get())
```

## Performance Considerations & System Resources

- **ThreadPoolExecutor:**
    - Best for I/O-bound tasks where waiting for external operations is the bottleneck (network requests, file I/O).
    - The ideal number of threads can often be higher than the number of CPU cores, as threads spend time waiting.
    - Keep in mind the **Global Interpreter Lock (GIL)** in CPython, which limits the true parallelism of CPU-bound tasks within a single process. For CPU-intensive work, `ProcessPoolExecutor` is generally better.
      
- **ProcessPoolExecutor:**
    - Best for CPU-bound tasks that can benefit from utilizing multiple CPU cores (complex calculations, data processing). Each process runs independently, bypassing the GIL.
    - Optimal number of processes is typically related to the number of available CPU cores. Going beyond this might introduce overhead without substantial performance gains due to increased context switching and IPC overhead.
    - Inter-process communication (IPC) can introduce overhead. Ensure that the work done by each process is substantial enough to outweigh this overhead. Avoid frequent and large data transfers between processes if possible.
      
- **System Resources:**
    - **CPU Cores:** Use `os.cpu_count()` to determine the number of logical CPU cores available. This is particularly relevant for setting a reasonable `max_workers` value for `ProcessPoolExecutor`.
      ```python
	    import os
	    print(os.cpu_count())  
		```
    - **Memory:** Each process in `ProcessPoolExecutor` has its own memory space. Running too many processes with memory-intensive tasks can lead to high memory consumption and potential system instability. Threads in `ThreadPoolExecutor` share the same memory space, so memory usage might be lower overall but requires careful management of shared resources.
    - **Context Switching:** Both excessive threads and processes can lead to increased context switching overhead, where the system spends more time switching between tasks than actually executing them. Finding the right balance is crucial. Monitor system performance to identify potential bottlenecks.


## Best Practices

* Choose executor wisely - use `ThreadPoolExecutor` for **I/O-bound** tasks and `ProcessPoolExecutor` for **CPU-bound** tasks for optimal performance.
- Tune `max_workers` - set the number of workers thoughtfully, considering workload and system resources. Start with CPU core count for processes, potentially more for threads (experiment).
- Thread safety - when threads share data, use `threading.Lock` or other synchronization tools  to prevent race conditions.
- Process communication - for inter-process data sharing, use `multiprocessing` tools (like `Queue`). Be aware of inter-process communication overhead.
- Handle Exceptions - use `try...except` within concurrent tasks to manage errors gracefully.
- Use `with` statement - this ensures proper resource clean-up as executor resources are releases when the block finishes. 
- Keep tasks independent - design tasks to minimize the need for complex inter-task communication.
- Consider `asyncio` - for complex I/O concurrency, explore `asyncio` as an alternative to thread pools.