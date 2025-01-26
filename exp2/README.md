# Program Statement: 
To implement UNIX operating system calls fork, exec, getpid, exit, wait, close, stat, opendir and readdir
# Program Description
  Description of `fork`, `exec`, `getpid`, `exit`, `wait`, `close`, `stat`, `opendir`, and `readdir` System Calls

- **`fork()`**: Creates a new process by duplicating the current process, returning `0` to the child and the child’s PID to the parent.  
- **`exec()`**: Replaces the current process image with a new program. Does not return on success.  
- **`getpid()`**: Retrieves the unique process ID of the calling process.  
- **`exit()`**: Terminates a process and releases its resources, returning a status code to the parent.  
- **`wait()`**: Suspends the calling process until a child process terminates, allowing retrieval of the child's exit status.  
- **`close()`**: Closes an open file descriptor to free associated resources.  
- **`stat()`**: Retrieves file or directory metadata, including size, permissions, and timestamps.  
- **`opendir()`**: Opens a directory stream for reading its entries.  
- **`readdir()`**: Reads individual entries from a directory stream, returning their details.  

These system calls are essential for process management, file handling, and directory navigation in UNIX-like operating systems.
