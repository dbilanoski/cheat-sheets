
To install:
```powershell
pip install pycryptodome
```

## Encrypting & Decrypting ENC files

In this example, key file with password is used for encryption. In steps:
* Password is then from the file.
* Salt is generated with `urandon` (or extracted from the enc file when decrypting).
* Password and salt are used to derive encryption key and initialization vector.
* Key and IV are used for AES encryption and decryption.

```python
# Imports
from Crypto.Cipher import AES
from Crypto.Hash import MD5
from Crypto.Util.Padding import unpad
from os import urandom

# Define function which creates key and initialization vector replicating the way EVP_BytesToKey in OpenSSL work. This is needed for encrytption.

def derive_key_and_iv(password: bytes, key_size: int, iv_size: int, salt: bytes | None = None) -> tuple[bytes, bytes]:

    """
    Derive the AES key and IV (Initialization Vector) from a password using MD5 hashing, replicating OpenSSL's EVP_BytesToKey method.

    The function keeps hashing the password and salt until it builds a big enough "random-looking" sequence of bytes, then slices it into a key and an IV just like OpenSSL expects.

    Params:
    - password: Password bytes used for key derivation.
    - key_size: Desired key size in bytes (32 bytes for AES-256).
    - iv_size: Desired IV size in bytes (16 bytes for AES).
    - salt: Optional salt bytes. If not provided, only password is used.

    Returns:
    - Tuple containing (key, iv).
    """

  
    # Collector of bytes to generate key and IV
    key_iv_material = b''

    # Last MD5 hash result holder, updated on each iteration
    previous_hash = b''

    # Keep generating MD5 hashes until we have enough bytes to build both the key and the IV.
    while len(key_iv_material) < (key_size + iv_size):

        if salt:
            # If salt was provided, include it in the hashing
            previous_hash = MD5.new(previous_hash + password + salt).digest()

        else:
            # If no salt, just hash password
            previous_hash = MD5.new(previous_hash + password).digest()


        # Append generated hash to the key_iv_material
        key_iv_material += previous_hash

    # First part of the byte sequence becomes the key
    key = key_iv_material[:key_size]

    # Next part becomes the initialization vector (IV)
    iv = key_iv_material[key_size:key_size + iv_size]

    # Return tuple of (key, iv)
    return key, iv

# Define encrypting function
def encrypt_openssl_aes256cbc(input_file_path: str, key_file_path: str, encrypted_file_path: str):
    """
    Encrypt a file using OpenSSL-compatible AES-256-CBC encryption with password read from key file.
    
	Params:
    - input_file_path: Path to the plaintext input file.
    - key_file_path: Path to the key (password) file.
    - encrypted_file_path: Path to save the encrypted output file.
    """
    # Read password from key file
    with open(key_file_path, 'rb') as key_file:
        password = key_file.read().strip()

    # Read input data to encrypt
    with open(input_file_path, 'rb') as input_file:
        plaintext_data = input_file.read()

    # Generate a random 8-byte salt
    salt = urandom(8)

    # Derive key and IV from password + salt
    key, iv = derive_key_and_iv(password, key_size=32, iv_size=16, salt=salt)

    # Set up AES cipher for encryption
    cipher = AES.new(key, AES.MODE_CBC, iv)

    # Pad the plaintext data to be a multiple of AES block size
    padded_plaintext = pad(plaintext_data, AES.block_size)

    # Encrypt the padded data
    encrypted_data = cipher.encrypt(padded_plaintext)

    # Write OpenSSL-compatible output: b"Salted__" + salt + ciphertext
    with open(encrypted_file_path, 'wb') as encrypted_file:
        encrypted_file.write(b'Salted__' + salt + encrypted_data)


# Define decrypting function

def decrypt_openssl_aes256cbc(encrypted_file_path: str, key_file_path: str, decrypted_file_path: str):
    """
    Decrypt a file encrypted with OpenSSL AES-256-CBC using a password read from a key file.

    Params:
    - encrypted_file_path: Path to the encrypted input file.
    - key_file_path: Path to the key (password) file.
    - decrypted_file_path: Path to save the decrypted output file.
    """
    # Read password from key file
    with open(key_file_path, 'rb') as key_file:
        password = key_file.read().strip()

    # Read the encrypted file content
    with open(encrypted_file_path, 'rb') as encrypted_file:
        encrypted_data = encrypted_file.read()

    # Check if file was salted (OpenSSL adds "Salted__" + salt bytes)
    if encrypted_data.startswith(b'Salted__'):
        salt = encrypted_data[8:16]
        encrypted_data = encrypted_data[16:]  # Remove header and salt from the data
        key, iv = derive_key_and_iv(password, key_size=32, iv_size=16, salt=salt)
    else:
        key, iv = derive_key_and_iv(password, key_size=32, iv_size=16)

    # Set up AES cipher for decryption
    cipher = AES.new(key, AES.MODE_CBC, iv)

    # Decrypt and remove padding
    decrypted_data = unpad(cipher.decrypt(encrypted_data), AES.block_size)

    # Write the decrypted data to output file
    with open(decrypted_file_path, 'wb') as output_file:
        output_file.write(decrypted_data)



# Example usage:

encrypt_openssl_aes256cbc(
    input_file_path='path/to/plain_file.gz',
    key_file_path='path/to/key_file.key',
    encrypted_file_path='path/to/encrypted_output.enc
    )

decrypt_openssl_aes256cbc(
    encrypted_file_path='path/to/encrypted_file.enc',
    key_file_path='path/to/key_file.key',
    decrypted_file_path='path/to/decrypted_output.gz'
	)

```

## Basic Concepts

**AES-256-CBC**
* AES (Advanced Encryption Standard) is a secure encryption algorithm. 
* **256** means a 256-bit key is used (very strong). 
* **CBC** (Cipher Block Chaining) is a mode where each block of plaintext is **XOR-ed** with the previous ciphertext block before encryption. This prevents identical plaintext blocks from producing identical ciphertext blocks, making it more secure.

**Key Derivation (EVP_BytesToKey)**
* A password by itself is usually too short to be a secure encryption key. 
* OpenSSL uses a method called `EVP_BytesToKey` to stretch the password into a strong key and IV. It does this by hashing the password repeatedly using **MD5** until enough bytes are created.

**MD5 Hash**
* MD5 (Message Digest 5) is a fast cryptographic hash function. It takes any input and produces a 16-byte (128-bit) output. 
* It’s _not considered secure_ anymore for new systems, but OpenSSL still uses it in old encryption modes like this one.

**Salt**
* Random data (usually 8 bytes) added to the password before key derivation. 
* This makes dictionary attacks harder because the same password will generate different encryption keys each time if salt is used. 
* In OpenSSL files, if a file is salted, it starts with a header `Salted__` followed by the 8-byte salt.

**Padding (PKCS7)**
* AES requires the data to fit exactly into fixed-size blocks (16 bytes each for AES). If the data isn’t a multiple of 16, extra bytes are added at the end to fill the last block. 
* During decryption, these padding bytes must be removed properly (called unpadding) to get the original data back.

**ENC File**
* `.enc` simply means "encrypted" file. It’s not a special file format, just a normal binary file with encrypted data inside. 
* `.enc` extension is a convention to remind you that it’s not directly readable.





## References

1. [Official PyCryptodome Docs](https://www.pycryptodome.org/src/features)