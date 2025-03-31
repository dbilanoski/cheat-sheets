# Python's Paramiko Library Notes

Widely-used library for SSH and SFTP connections.
- Support for SSH2 protocol, including file transfer using SFTP.
- Secure authentication with username/password or private/public keys.
- Easy-to-use interface for file uploads, downloads, and directory management.


## Basic Usage

### Installation

```powershell
pip install paramiko
```
## Upload to SFTP Example

```python
import paramiko

# SFTP configuration
hostname = "sftp.example.com"
port = 22
username = "your_username"
password = "your_password"
local_file = "path/to/local/file.txt"
remote_path = "/path/on/server/file.txt"

# Connect and upload file
try:
    # Create an SSH client
    ssh = paramiko.SSHClient()
    # Automatically add host keys
    # https://docs.paramiko.org/en/latest/api/hostkeys.html
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())  
   
    ssh.connect(hostname, port=port, username=username, password=password)

    # Open an SFTP session
    sftp = ssh.open_sftp()

    # Upload the file
    sftp.put(local_file, remote_path)
    print(f"Successfully uploaded {local_file} to {remote_path}")

    # Close the SFTP session and SSH connection
    sftp.close()
    ssh.close()
except Exception as e:
    print(f"An error occurred: {e}")
```


## Upload to SFTP From In-Memory Data With Pandas DataFrame Example

Here is a scenario where Pandas dataframe needs to be saved as an Excel file to a SFTP location where:
1. We write it as an Excel file to a file like object in memory using Bytes.IO
2. Then we use sftp.file() context to write the values to a real file on the SFTP destination


```python
import paramiko
import pandas as pd
import io

# SFTP configuration
hostname = "sftp.example.com"
port = 22
username = "your_username"
password = "your_password"
remote_path = "/path/on/server/file.txt"

# Sample DataFrame
df = pd.DataFrame({'A': [1, 2, 3], 'B': [4, 5, 6]})

# Initiate an io.BytesIO file-like memory chunk
excel_buffer = io.BytesIO()
# Write DataFrame as Excel file to that memory chunk
dataframe.to_excel(excel_buffer, index=False, engine="openpyxl")
# Reset buffer position
excel_buffer.seek(0)

# Connect and upload file
try:
    # Create an SSH client
    ssh = paramiko.SSHClient()
    # Add the host name and key automatically to the local .hostkey object
    # https://docs.paramiko.org/en/latest/api/hostkeys.html
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())  
	# Connect to the server
    ssh.connect(hostname, port=port, username=username, password=password)

    # Open an SFTP session
    sftp = ssh.open_sftp()

    # Upload in-memory file using sftp.file()
    sftp.file(remote_path, "wb") as remote_file:
	    remote_file.write(excel_buffer.getvalue())
	    
    print(f"Successfully uploaded {local_file} to {remote_path}")

    # Close the SFTP session and SSH connection
    sftp.close()
    ssh.close()
    
except Exception as e:
    print(f"An error occurred: {e}")
```