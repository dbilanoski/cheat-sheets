# OpenSSL Notes

Open-source toolkit mainly used to **secure data** in transit or at rest (for example, encrypting backups, certificates for websites etc.)

- **Encrypting and decrypting** files
- **Generating keys** and **certificates**
- **Creating secure communications** (SSL/TLS)
- **Hashing** and **signing** data

It brings both library (for dev work) and command-line tools.


## Handling Encryption & Hashing

- Encryption - scrambling data so only someone with the correct password/key can read it.
- Decryption - turning encrypted data back to its original form.
- Symmetric encryption - same password/key is used for both encrypting and decrypting.

OpenSSL’s `enc` command is mainly for symmetric encryption of files.
### Commonly Used OpenSSL CLI Commands

|Action|Command Example|
|:--|:--|
|**Encrypt a file**|`openssl enc -aes-256-cbc -salt -in plain.txt -out encrypted.enc -k mypassword`|
|**Decrypt a file**|`openssl enc -aes-256-cbc -d -in encrypted.enc -out plain.txt -k mypassword`|
|**Encrypt with a key file**|`openssl enc -aes-256-cbc -salt -in plain.txt -out encrypted.enc -kfile key.txt`|
|**Decrypt with a key file**|`openssl enc -aes-256-cbc -d -in encrypted.enc -out plain.txt -kfile key.txt`|
|**Hash a file (SHA256)**|`openssl dgst -sha256 file.txt`|
|**Generate a random key**|`openssl rand -hex 32 > key.txt`|

### Working With `.enc` Files

**`.enc`** means **"encrypted" file**. It’s not a special file format, just a normal binary file with encrypted data inside. The `.enc` extension is a convention to remind you that it’s not directly readable.

To encrypt (and create) them:

	```powershell
	openssl enc -aes-256-cbc -salt -in myfile.txt -out myfile.enc -k mypassword
	```

To decrypt and recover original file:

	```powershell
	openssl enc -aes-256-cbc -d -in <inputfile> -out <outputfile> -kfile <keyfile> -md md5
	```

Where:
- `enc`:  use OpenSSL symmetric encryption tool.
- `-aes-256-cbc`_ use AES algorithm with 256-bit key and CBC mode.
- `-d`: decrypt the file.
- `-in <inputfile>`: input encrypted file.
- `-out <outputfile>`: output decrypted file.
- `-kfile <keyfile>`: read password from a file.
- `-md md5`: use MD5 hashing to derive the encryption key from the password.


### Best Practice & Example Commands

- Always **encrypt with `-salt`** for better security.
- Always **keep your password or key file safe** — without it, decryption is impossible.
- **MD5** is old and not recommended for _new_ secure applications, but it is still commonly used for **legacy systems** (like SSIS processes you are migrating).
- OpenSSL encrypted files are just **binary data** — you need correct parameters to decrypt them properly.

| Action                         | Command                                                                    |
| :----------------------------- | :------------------------------------------------------------------------- |
| Encrypt a file with a password | `openssl enc -aes-256-cbc -salt -in file.txt -out file.enc -k password`    |
| Decrypt a file with a password | `openssl enc -aes-256-cbc -d -in file.enc -out file.txt -k password`       |
| Encrypt a file with a key file | `openssl enc -aes-256-cbc -salt -in file.txt -out file.enc -kfile key.txt` |
| Decrypt a file with a key file | `openssl enc -aes-256-cbc -d -in file.enc -out file.txt -kfile key.txt`    |

## References

1. [Official OpenSSL Docs](https://docs.openssl.org/3.4/man1/openssl/)