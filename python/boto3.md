# AWS Interaction With Python's Boto3

Boto3 is the official AWS SDK for Python allowing interaction with AWS services like S3, DynamoDB, EC2 etc. It provides both low-level access via clients and high-level object-oriented access via resources.

To skip theory and see full example, jump to [[#Full Example With Client And Explicit Authentication]]

## Basic Usage

### Installation

```powershell
pip install boto3
```

### Connecting To AWS

Two options for talking to AWS:
1. Using S3 Client, which is a low-level API providing direct access to AWS services using JSON responses.
2. Using S3 Resource, which is a high-level API providing an object-oriented interfaces with more "Pythonic" access to AWS services.

Two options to provide credentials to the S3 Client or a Resource:
1. Using Session (useful for multiple accounts or regions).
2. Providing credentials directly (which we'll be doing later in examples).
   
#### Example Using A Session

When you create a session using `boto3.Session()`, it attempts to authenticate using credentials from the following sources **in order**:

1. **Explicit credentials** in the `Session()` constructor (if provided).
2. **Environment variables** (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSION_TOKEN`).
3. **AWS credentials file** (`~/.aws/credentials` or `C:\Users\USERNAME\.aws\credentials` on Windows).
4. **AWS config file** (`~/.aws/config` for default profile settings).
5. **IAM roles** (for EC2, Lambda, etc.)—if running in an AWS environment, Boto3 will try to retrieve credentials from the instance profile or container credentials.
   

Say we have environment variables configured in Windows:

```powershell
$Env:AWS_ACCESS_KEY_ID = "my-key"
$Env:AWS_SECRET_ACCESS_KEY = "my-secret"
```

Then this script uses the via instantiated session.

```python
import boto3

# Initialize a session (useful for multiple accounts/regions)
session = boto3.Session(profile_name='default', region_name='us-east-1')

# Create an S3 client (low-level API)
# - Provides direct access to AWS services using JSON responses.
# - Best for performance-critical operations where full control is needed.
s3_client = session.client('s3',
    region_name='us-west-2',  # Optional, specify a different AWS region
    endpoint_url='https://custom-s3-endpoint.com',  # Optional, use a custom S3-compatible service
    use_ssl=True,  # Optional, enable or disable SSL
    verify=True,  # Optional, verify SSL certificates (can be a path to a CA bundle)
    config=boto3.session.Config(retries={'max_attempts': 5})  # Optional, configure retry logic
)

# Create an S3 resource (high-level API)
# - Provides an object-oriented interface with Pythonic access to AWS services.
# - Simplifies resource management, reducing boilerplate code.
s3_resource = session.resource('s3',
    region_name='us-west-2',
    endpoint_url='https://custom-s3-endpoint.com'
)
```

#### Example Without a Session (Manually Providing Credentials)

Useful when:
- You are not using a pre-configured AWS profile.
- You want to explicitly define credentials in your code (though it's safer to use environment variables or IAM roles).
- You need to connect to a custom or on-premises S3-compatible service.

```python
import boto3

s3_client = boto3.client('s3',
    aws_access_key_id='your-access-key',
    aws_secret_access_key='your-secret-key',
    region_name='us-west-2',# Optional
    endpoint_url='https://custom-s3-endpoint.com', # Optional
    use_ssl=True,  # Optional
    verify=True,  # Optional
    config=boto3.session.Config(retries={'max_attempts': 5}) # Optional
)

s3_resource = boto3.resource('s3',
    aws_access_key_id='your-access-key',
    aws_secret_access_key='your-secret-key',
    region_name='us-west-2', # Optional
    endpoint_url='https://custom-s3-endpoint.com' # Optional
)
```

We'll be using Client approach in all examples.
### CRUD Operations On S3

```python
# Upload a file
s3_client.upload_file(file_name, bucket_name, object_name)

# Download a file
s3_client.download_file(aws_bucket_name, object_name, file_name)

# List files on bucket
response = s3_client.list_objects_v2(Bucket=bucket_name)
files: list = [obj['Key'] for obj in response.get('Contents', [])]

# Copy a file within or between buckets
copy_source: dict = {'Bucket': source_bucket, 'Key': source_key}
s3_client.copy(copy_source, destination_bucket, destination_key)

# Deletes a file
s3_client.delete_object(Bucket=bucket_name, Key=object_name)

# List all buckets
response = s3_client.list_buckets()
buckets: list = [bucket['Name'] for bucket in response['Buckets']]

# Get Object metadada
response = s3_client.head_object(Bucket=bucket_name, Key=object_name)
print(response)

# Get bucket location (region)
response = s3_client.get_bucket_location(Bucket=bucket_name)
print(response.get('LocationConstraint')
```


## Full Example With Client And Explicit Authentication

```python
import boto3

# Declare credentials and config data
aws_access_key_id: str = "your-access-key"
aws_secret_access_key: str = "your-secret-key"
aws_bucket_name: str = "your-bucket-name"
aws_root_dir: str = "resources/example"

# Create an S3 client with explicit credentials
s3_client = boto3.client('s3',
    aws_access_key_id=aws_access_key_id,
    aws_secret_access_key=aws_secret_access_key
)

# Upload a file to S3 with error handling and retry logic
def upload_file(file_name, object_name=None, max_retries=2):
    # If you did not provide a destination path (like: resources/example/filename.txt), take name of the file you're uploading to
    if object_name is None:
        object_name = file_name
    
    # Initiate attempts counter
    attempts = 0
    # Iterate while we're below max_retries
    while attempts < max_retries:
	    # Try uploadin
        try:
            s3_client.upload_file(file_name, aws_bucket_name, object_name)
            print(f"Successfully uploaded {file_name} to {aws_bucket_name}/{object_name}")
            return
        # Catch client related errors
        except botocore.exceptions.ClientError as e:
	        # Extract error code
            error_code = e.response['Error']['Code']
            # If code is related to restrictions where it does not make sense to retry, exit
            if error_code in ["AccessDenied", "UnauthorizedOperation"]:
                print("Permission error: Check your IAM policy.")
                return
            # Otherwise, increase the counter by 1 and try again
            else:
                print(f"Upload failed (attempt {attempts + 1}): {e}")
                attempts += 1
                # Wait before retrying
                time.sleep(2)  
    print("Max retries reached. Upload failed.")


# List files in S3 bucket
def list_files():
    response = s3_client.list_objects_v2(Bucket=aws_bucket_name)
    return [obj['Key'] for obj in response.get('Contents', [])]

# Download a file from S3
def download_file(object_name, file_name):
    s3_client.download_file(aws_bucket_name, object_name, file_name)

# Delete a file from S3
def delete_file(object_name):
    s3_client.delete_object(Bucket=aws_bucket_name, Key=object_name)

# Example Usage
if __name__ == "__main__":
    upload_file("test.txt")
    print("Files in bucket:", list_files())
    download_file("test.txt", "downloaded_test.txt")
    delete_file("test.txt")

```

## Terminology

- **Client vs. Resource**: Clients provide low-level API calls, while resources offer a more Pythonic approach.
- **Low-level API (Client)**: Uses direct API calls for greater flexibility and efficiency.
- **High-level API (Resource)**: Provides an object-oriented interface for easier interaction with AWS services.
- **Bucket**: A top-level storage container in S3 that holds objects.
- **Object**: A single file stored in S3, along with its metadata.
- **Key**: The unique identifier for an object within a bucket (like a file path).
	- There will be no folders since we're working with objects, but each subsequent part of the key string delimited by forward slash (/) will be shown as folder for simplicity
	- This means that uploading files automatically creates all needed "folders" in the path provided in the key value.
- **Region**: The AWS geographical location where a bucket is hosted (e.g., `us-east-1`).
- **IAM Roles & Permissions**: Control access to AWS services ensuring the necessary permissions are in place.

## Resources

1. [Official Boto3 Documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)
