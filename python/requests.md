
# Requests Note

`requests` is the standard library for making HTTP requests in Python. It abstracts the complexities of making requests behind a simple API, allowing you to send HTTP/1.1 requests extremely easily. It handles keep-alive and HTTP connection pooling automatically.

## Basic Usage

Here is how to perform the most common HTTP actions.

### Get (Fetching Data)

Used to retrieve data from a server. Data is passed in the URL.

```python
import requests

# The endpoint you want to reach
url = "https://api.example.com/search"

# Parameters are passed as a dictionary
# 'requests' automatically encodes them into the URL: ?q=python&page=1
payload = {"q": "python", "page": 1}

# Execute the GET request
response = requests.get(url, params=payload)

# Check if the request succeeded (200 OK)
if response.status_code == 200:
    # Parse the JSON response into a Python dictionary
    data = response.json()
    print(data)
```


### POST (Creating Data )

Used to send data to the server, usually to create a new resource. The standard modern way is sending JSON.

```python
url = "https://api.example.com/users"

# Data to be sent (as a Python dictionary)
new_user = {"username": "jdoe", "email": "john@example.com"}

# 'json=' automatically serializes the dict to a JSON string 
# AND sets the Content-Type header to 'application/json'
response = requests.post(url, json=new_user)

# A robust way to check for errors (raises exception for 4xx/5xx codes)
response.raise_for_status()
```


But for older web forms or specific APIs (like OAuth token endpoints) that expect form-encoded data, not JSON.

```python
url = "https://example.com/login.php"

# Data acts like a filled-out HTML form
form_data = {"user": "admin", "password": "123"}

# 'data=' sends this as 'application/x-www-form-urlencoded'
# The body will look like: user=admin&password=123
response = requests.post(url, data=form_data)
```

### DELETE (Removing Data)

Used to delete a resource from the server, usually identified by its ID in the URL.

```python
# The resource to delete (e.g., User with ID 10)
url = "https://api.example.com/users/10"

# Execute the DELETE request
response = requests.delete(url)

# Check status. 
# 204 No Content is the standard success code for DELETE (means "Done, nothing to return")
if response.status_code == 204:
    print("User deleted successfully.")
else:
    response.raise_for_status()
```

### PATCH (Partial Update)
Used to update _specific fields_ of a resource without replacing the whole thing.

```python
url = "https://api.example.com/users/10"

# We only want to change the email, leaving name/address alone
partial_update = {"email": "new_email@example.com"}

# Execute PATCH. The server merges this change into the existing record.
response = requests.patch(url, json=partial_update)

print(response.json()) 
# Output might be: {"id": 10, "name": "Old Name", "email": "new_email@example.com"}
```

## PUT (Full Replacement)

Used to _completely replace_ a resource. If you omit a field, the server usually sets it to null/default.

```python
url = "https://api.example.com/users/10"

# We must provide the COMPLETE representation of the user
full_replacement = {
    "username": "jdoe",
    "email": "new_email@example.com",
    "is_active": True,
    "role": "admin" 
}

# Execute PUT. The old record is discarded and replaced by this one.
response = requests.put(url, json=full_replacement)
```

## Authentication Pattern (Fetching & Using Tokens)

This is the most common pattern for modern APIs:

1. **Login** (usually Basic Auth) to get a Token.
2. **Use Token** (Bearer Auth) for all subsequent requests.

```python
import base64

# --- STEP 1: GET THE TOKEN ---
auth_url = "https://api.example.com/auth/token"
username = "my_user"
password = "my_password"

# Create the Basic Auth string (user:pass)
# requests.auth.HTTPBasicAuth can do this automatically, but here is the manual way:
# Create text string
auth_str = f"{username}:{password}"
# Base64 math cannot work with strings, so we convert it to raw binary data (bytes)
auth_bytes = auth_str.encode("ascii")
# base64.b64encode() - The Base64 algorithm runs on those bytes and produces new Bytes that represent the encoded value
# Then .decode() we need becase `requests` library expects headers to be strings, so we convert it back to a string
base64_auth = base64.b64encode(auth_bytes).decode("ascii")

# Headers for the login request
login_headers = {
    "Authorization": f"Basic {base64_auth}"
}

# Send POST to get the token
response = requests.post(auth_url, headers=login_headers)
response.raise_for_status()

# Extract token from response (e.g., {"access_token": "abc-123-xyz"})
token = response.json().get("access_token")
print(f"Got Token: {token}")


# --- STEP 2: USE THE TOKEN ---
api_url = "https://api.example.com/protected/resource"

# Standard Bearer Token header format
api_headers = {
    "Authorization": f"Bearer {token}",
    "Content-Type": "application/json"
}

# Now we can access protected endpoints
data_response = requests.get(api_url, headers=api_headers)
print(data_response.json())
```

## How Requests Build HTTP Packet

HTTP requests have two main places to put data:

1. **The URL (Address Bar):** This is visible. We use this for **Filters** (e.g., "Find user with ID 1"). In Python `requests`, this is the `params=` argument.
2. **The Body (Envelope Content):** This is hidden inside the packet. We use this for **Data** (e.g., "Change name to John"). In Python `requests`, this is `json=` or `data=`.

### 1. `params=` → The Address Bar (URL)

- **What it does:** Appends data to the URL after a `?`.
- **Packet Location:** The Request Line (Header).
- **Use Case:** Filtering, Sorting, Searching (mostly GET).
    
### 2. `json=` → The Envelope (Body) - Modern

- **What it does:** Converts your dict to a string, puts it in the body, and sets header `Content-Type: application/json`.
- **Packet Location:** Body.
- **Use Case:** APIs, Creating Users, Uploading Configs.
    
### 3. `data=` → The Envelope (Body) - Legacy/Form

- **What it does:** Encodes dict as form keys, puts it in the body, and sets header `Content-Type: application/x-www-form-urlencoded`.
- **Packet Location:** Body.
- **Use Case:** HTML Form submissions, OAuth Logins, File Uploads.

 
|**Argument**|**Python Input**|**HTTP Result**|**Common Method**|
|---|---|---|---|
|`params`|`{'id': 1}`|URL: `.../api?id=1`|`GET`|
|`json`|`{'id': 1}`|Body: `{"id": 1}`|`POST`, `PUT`, `PATCH`|
|`data`|`{'id': 1}`|Body: `id=1`|`POST` (Legacy)|
## Best Practices

1. Always use `raise_for_status()`** Instead of writing `if response.status_code == 200`, use `response.raise_for_status()`. It throws a specific error if the request failed, which you can catch and log.
   
2. **Never forget `timeout`** By default, `requests` will hang **forever** if the server accepts the connection but sends no data. Always set a timeout (in seconds).
   `requests.get('https://github.com', timeout=10)`
   
3. **Use `requests.Session()` for efficiency** If you are making multiple requests to the same host, use a Session. It keeps the TCP connection open, making subsequent requests significantly faster.
   
   ```python
	# Instead of requests.get(), use:
	session = requests.Session()
	session.get('https://api.com/1')
	session.get('https://api.com/2') # Reuses connection!
   ```

4. **Handle Authentication gracefully** Don't bake headers manually if you can avoid it.
   
   ```
   # Bad headers = {"Authorization": "Bearer 123"} # Good session.auth = ('user', 'pass') # Basic Auth # or session.headers.update({"Authorization": "Bearer 123"}) # Sets it for all future requests
   ```

