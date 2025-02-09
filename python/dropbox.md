# Dropbox API With Python

To be able to work with Dropbox API using the Dropbox Python library, you'll need some configuration on the cloud side first:

1. Create a Dropbox [account](https://www.dropbox.com/register).
2. Visit [developer section.](https://www.dropbox.com/developers) 
	1. Choose **My apps** on the lefthand side of the dashboard and click **Create app**.
	2. Choose API type and the access you need, then name the application.
	3. In the app page, find "OAuth 2" section, then under "Generated access token" click "Generate" button. Save the `access_token` as you'll be needing it in the Python script.


## Basic Usage

### Installation 

```powershell
pip install dropbox
```

### Dropbox Operations

These are written as functions for easier forking.

```python
# Import dropbox library
import dropbox
# These two are optional - used for type annotation and try-except specific error catching.
from dropbox.exceptions import ApiError
from dropbox.files import FileMetadata, FolderMetadata, SharedLinkFileInfo

### STEP 1: DEFINE FUNCTIONS ###

# Connect to Dropbox client
def connect_to_dropbox(token: str | None) -> dropbox.Dropbox:
  """
  Instantiates Dropbox API connection.
  Params:
    - token: Dropbox accesss token.
  """
  return dropbox.Dropbox(token)


# File or folder uploading
def upload_file(dropbox_api: dropbox.Dropbox, local_file_path: str, remote_file_path: str) -> None:
  """
  Uploads a file from local storage to Dropbox.

  Params:
    - dropbox_api (dropbox.Dropbox): Instantiated Dropbox client.
    - local_file_path (str): Path to the local file.
    - remote_file_path (str): Path in Dropbox where the file will be uploaded.
  """

  if not os.path.exists(local_file_path):
    print(f"File at {local_file_path} does not exists.")
    return None

  try:
    with open(local_file_path, "rb") as file:
      dropbox_api.files_upload(file.read(), remote_file_path) # type: ignore
      print(f"Uploaded {local_file_path} to {remote_file_path} successfully.")
  except Exception as e:
    print(f"Something went wrong during upload, error: {str(e)}")


# Downloading
def download_file(dropbox_api: dropbox.Dropbox, remote_file_path: str, local_file_path: str) -> None:
  """
  Dowloads a file from Dropbox to local storage.

  Params:
    - dropbox_api (dropbox.Dropbox): Instantiated Dropbox client.
    - remote_file_path (str): Path to a Drobox file to be dowloaded.
    - local_file_path (str): Path in the local storage where the file will be downloaded.
  """

  try:
    dropbox_api.files_download_to_file(local_file_path, remote_file_path)
    print(f"Downloaded {remote_file_path} to {local_file_path} successfully.")
  except Exception as e:
    print(f"Something went wrong during download, error: {str(e)}")


# List files and folders
def list_files(dropbox_api: dropbox.Dropbox, remote_folder_path: str) -> list | None:
  """
  Lists files in a Dropbox folder.

  Params:
    - dropbox_api (dropbox.Dropbox): Instantiated Dropbox client.
    - remote_folder_path (str): Path to the Dropbox folder.
  """

  try:
    # Get api response
    response: FolderMetadata | None = dropbox_api.files_list_folder(remote_folder_path)

    # Return a list on file\folder names
    if response:
      return [entry.name for entry in response.entries]
  except Exception as e:
    print(f"Error listing folder {remote_folder_path}: {e}")


# Delete file or folder
def delete_item(dropbox_api: dropbox.Dropbox, remote_item_path: str) -> None:
  """
  Deletes a file or folder from Dropbox.

  Params:
    - dropbox_api (dropbox.Dropbox): Instantiated Dropbox client.
    - remote_item_path (str): Path in Dropbox to the file to delete.
  """

  try:
    dropbox_api.files_delete_v2(remote_item_path)
    print(f"Deleted {remote_item_path} successfully.")
  except Exception as e:
    print(f"Error deleting file {remote_item_path}: {e}")


# Create folder
def create_folder(dropbox_api: dropbox.Dropbox, remote_folder_path: str) -> None:
  """
  Creates a folder on Drobox.

  Params:
   - dropbox_api (dropbox.Dropbox): Instantiated Dropbox client.
   - remote_folder_path (str): Path in Dropbox to create the folder to.
  """

  try:
    dropbox_api.files_create_folder_v2(remote_folder_path)
    print(f"Folder created at {remote_folder_path}")
  except ApiError as e:
    if e.error.get_path().is_conflict():
      # Means folder already exists
      print(f"Folder {remote_folder_path} already exists.")
    else:
      print(f"Something went wrong while trying to create folder {remote_folder_path}, error: {e}.")


# Create shared link
def create_shared_link(dropbox_api: dropbox.Dropbox, remote_item_path: str) -> str | None:
  """
  Creates a shareable link to a file or folder on Dropbox.

  Params:
    - dropbox_api (dropbox.Dropbox): Instantiated Dropbox client.
    - remote_item_path (str): Path to a Drobpox file or folder.
  """

  try:
    shared_link_metadata: SharedLinkFileInfo | None = dropbox_api.sharing_create_shared_link_with_settings(remote_item_path)

    # If we have response, print it
    if shared_link_metadata:
      print(f"Shared link created: {shared_link_metadata.url}")
      return shared_link_metadata.url
  except ApiError as e:
    # In case link already exists, let's pull that one and return it
    if e.error.is_shared_link_already_exists():
      links: SharedLinkFileInfo | None = dropbox_api.sharing_list_shared_links(path=remote_item_path)
      if links:
        print(f"Existing shared link: {links.links[0].url}")
        return links.links[0].url
    print(f"Error creating shared link for {remote_item_path}: {e}")
    return None


### STEP 2: USAGE ###

# Add your access token
DROPBOX_API_TOKEN: str = "your_access_token"

# Instantiate dbx client (which we then pass to other function for interacting with the Dropbox API)
dropbox_api: dropbox.Dropbox = connect_to_dropbox(DROPBOX_API_TOKEN)

# Create folder
create_folder(dropbox_api,"/test_folder")

# Upload file
upload_file(dropbox_api,"test.txt", "/test_folder/file.txt")

# Get shared link to that file
shared_link : str | None = create_shared_link(dropbox_api, "/test_folder/file.txt")
print(shared_link)

# Download file
download_file(dropbox_api, "/test_folder/file.txt", "downlaoded_file.txt")

# Delete file
delete_item(dropbox_api, "/test_folder/file.txt")

# Delete folder
delete_item(dropbox_api, "/test_folder")
```


## References

1. [Official Dropbox Python Library documentation](https://www.dropbox.com/developers/documentation/python#tutorial)
2. [Dropbox Developers console where you configure you app and access](https://www.dropbox.com/developers)