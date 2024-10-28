# Python's Google Cloud Storage Library Notes

Example showing basic CRUD operations as separate functions with this steps:
- **Instantiating the Client**: `get_storage_client()` initializes the Google Cloud Storage client using the application’s credentials, allowing interactions with Google Cloud Storage.
- **Uploading a File**: `upload_file_to_bucket()` uploads a local file (`source_file_name`) to a specified location (`destination_blob_name`) within the bucket (`bucket_name`).
- **Downloading a File**: `download_file_from_bucket()` downloads a file from the bucket (`source_blob_name`) to a local path (`destination_file_name`).
- **Copying a File**: `copy_file_within_bucket()` copies a file from one location (`source_blob_name`) to another (`destination_blob_name`) within the same bucket, creating a duplicate file.
- **Moving (Renaming) a File**: `move_file_within_bucket()` first copies the file to the new location (`destination_blob_name`) and then deletes the original (`source_blob_name`), effectively moving the file.

```python
# Import library
from google.cloud import storage

# Replace with your details
BUCKET_NAME = 'your-bucket-name'
UPLOAD_FILE_PATH = 'local/path/to/upload_file.txt'
DOWNLOAD_FILE_PATH = 'local/path/to/download_file.txt'
SOURCE_BLOB_NAME = 'subfolder/uploaded_file.txt'  # Blob name in bucket
DEST_BLOB_NAME = 'subfolder/moved_file.txt'  # New destination blob name

# Step 1: Instantiate the Client
def get_storage_client():
    client = storage.Client()  # Creates Google Cloud Storage client
    print("Google Cloud Storage client created successfully.")
    return client

# Step 2: Upload a File to a Specified Path
def upload_file_to_bucket(bucket_name, source_file_name, destination_blob_name):
    client = get_storage_client()
    bucket = client.bucket(bucket_name)
    blob = bucket.blob(destination_blob_name)  # Points to the destination file in bucket
    
    blob.upload_from_filename(source_file_name)  # Uploads file to bucket
    print(f"File {source_file_name} uploaded to {destination_blob_name} in bucket {bucket_name}.")

# Step 3: Download a File from Bucket to Local Path
def download_file_from_bucket(bucket_name, source_blob_name, destination_file_name):
    client = get_storage_client()
    bucket = client.bucket(bucket_name)
    blob = bucket.blob(source_blob_name)  # Refers to the file to be downloaded in bucket
    
    blob.download_to_filename(destination_file_name)  # Saves to local file path
    print(f"Blob {source_blob_name} downloaded to {destination_file_name}.")

# Step 4: Copy a File within the Bucket
def copy_file_within_bucket(bucket_name, source_blob_name, destination_blob_name):
    client = get_storage_client()
    bucket = client.bucket(bucket_name)
    source_blob = bucket.blob(source_blob_name)  # Reference to original file (blob)
    
    bucket.copy_blob(source_blob, bucket, destination_blob_name)  # Copies file within bucket
    print(f"Blob {source_blob_name} copied to {destination_blob_name} in bucket {bucket_name}.")

# Step 5: Move (Rename) a File within the Bucket
def move_file_within_bucket(bucket_name, source_blob_name, destination_blob_name):
    client = get_storage_client()
    bucket = client.bucket(bucket_name)
    source_blob = bucket.blob(source_blob_name)  # Reference to original file
    
    bucket.copy_blob(source_blob, bucket, destination_blob_name)  # Copies blob to new name
    source_blob.delete()  # Deletes original blob after copying to complete move
    print(f"Blob {source_blob_name} moved to {destination_blob_name} in bucket {bucket_name}.")

# Example Usage
upload_file_to_bucket(BUCKET_NAME, UPLOAD_FILE_PATH, SOURCE_BLOB_NAME)
download_file_from_bucket(BUCKET_NAME, SOURCE_BLOB_NAME, DOWNLOAD_FILE_PATH)
copy_file_within_bucket(BUCKET_NAME, SOURCE_BLOB_NAME, "subfolder/copied_file.txt")
move_file_within_bucket(BUCKET_NAME, SOURCE_BLOB_NAME, DEST_BLOB_NAME)

```

## Terminology

- **Client**: The object that connects to Google Cloud Storage and manages interactions with the service providing methods to speak with the service. 
- **Bucket**: A container in Google Cloud Storage that holds objects (files). 
	- Each bucket has a globally unique name and can store an unlimited number of objects.
- **Blob**: An object or file stored within a bucket. 
	- Blobs are the fundamental data entities in Google Cloud Storage, and each blob has a unique name within its bucket, which can include "folder" paths (e.g., `subfolder/uploaded_file.txt`).  
	- Those paths are "flat" object names, there are no real folders just parts of the name.
- **Destination Blob**: The path and name for the target location of a file within the bucket. 
	- When copying or moving a file, you specify a new blob name as the destination.
- **Source Blob**: The path and name for the original file within the bucket.


## References
1. Google docs [here](https://cloud.google.com/python/docs/reference/storage/latest)
2. Python docs [here](https://pypi.org/project/google-cloud-storage/).