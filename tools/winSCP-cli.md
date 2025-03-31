# WinSCP Command Line Note


WinSCP is a popular open-source SFTP, SCP, FTPS, and FTP client for Windows that allows secure file transfers between local and remote systems. It also includes a command-line interface (CLI) that enables automation and scripting for file transfers.


## Authentication
### Authentication with Username and Password

To authenticate using a username and password, use the following command:

```cmd
winscp.com /command "open sftp://username:password@hostname" "exit"
```


- `winscp.com` - Starts the WinSCP command-line interface.
- `open sftp://username:password@hostname` - Connects to the remote server via SFTP.
- `exit` - Exits the session.

Note: If the password contains special characters, enclose it in double quotes.

### Authentication with Local Certificate File

To authenticate using a private key file:

```cmd
winscp.com /command "open sftp://username@hostname/ -privatekey=C:\path\to\key.ppk" "exit"
```

- `-privatekey=C:\path\to\key.ppk` - Specifies the path to the private key file for authentication.

Note: the private key should be in PuTTY's PPK format. Convert it if necessary using PuTTYgen.


## Basic CRUD
### Uploading Data (Creating Remote Folder if Needed)

To upload a file and create a folder if it doesn't exist:

```cmd
winscp.com /command "open sftp://username:password@hostname" "mkdir /remote/path/newfolder" "put C:\local\file.txt /remote/path/newfolder/" "exit"
```


- `mkdir /remote/path/newfolder` - Creates a remote directory (if it does not exist).
- `put C:\local\file.txt /remote/path/newfolder/` - Uploads the specified file to the remote directory.


### Deleting Remote Data

To delete a file from the remote server:

```cmd
winscp.com /command "open sftp://username:password@hostname" "rm /remote/path/file.txt" "exit"
```


- `rm /remote/path/file.txt` - Deletes the specified file from the remote server.

To delete a folder and its contents:

```cmd
winscp.com /command "open sftp://username:password@hostname" "rmdir /remote/path/folder" "exit"
```

**Note**: Make sure path is correct, deletion is unreversable.

### Downloading Data

To download a file from the remote server:

```cmd
winscp.com /command "open sftp://username:password@hostname" "get /remote/path/file.txt C:\local\destination\" "exit"
```

- `get /remote/path/file.txt C:\local\destination\` - Downloads the specified file to the local system.

To download an entire folder:

```cmd
winscp.com /command "open sftp://username:password@hostname" "get -r /remote/path/folder C:\local\destination\" "exit"
```


- `get -r` - Recursively downloads the folder and its contents.


## References

* [WinSCP official documentation](https://winscp.net/).