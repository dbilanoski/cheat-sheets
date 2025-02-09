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