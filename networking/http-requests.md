# HTTP Basics

HTTP (Hypertext Transfer Protocol) is the foundation of data communication on the web. When you interact with websites or APIs, your browser or application sends **HTTP requests** to servers, and the servers respond with **HTTP responses**.

## Components Of An HTTP Request

An HTTP request typically contains the following parts:

- **Request Method:** Defines the type of operation you want to perform.
    - Common methods:
        - `GET`: Retrieve data from the server.
        - `POST`: Send data to the server (often used for submitting forms or sending API data).
        - `PUT`: Update existing resources on the server.
        - `DELETE`: Remove a resource from the server.
- **URL (Uniform Resource Locator):** The web address or API endpoint where the request is being sent.
    - Example: `https://api.example.com/data`
- **Headers:** Metadata about the request (like authentication, content type, etc.).
    - `Content-Type: application/json` – Specifies the format of the request body.
	- `Accept: application/json` – Informs the server of the expected response format.
	- `Authorization: Bearer <token>` – Sends credentials for authentication (token-based).
	- `User-Agent: Mozilla/5.0` – Identifies the client making the request.
	- `Cache-Control: no-cache` – Prevents caching; forces fresh requests.
	- `Referer: https://www.google.com/` – Indicates the origin of the request.
	- `Cookie: sessionId=abc123` – Sends cookies from the client to the server.
	- `Content-Length: 348` – Specifies the size of the request body in bytes.
	- `Host: api.example.com` – Specifies the server domain name.
	- `Origin: https://client-website.com` – Identifies the origin in CORS requests.
	- `X-Requested-With: XMLHttpRequest` – Indicates an Ajax request.
	- `Accept-Encoding: gzip` – Lists supported content encoding formats for compression.
	- `If-Modified-Since: Tue, 15 Nov 2023 12:45:26 GMT` – Requests the resource only if modified after a certain date.
- **Body (Optional):** The actual data being sent with the request (typically for `POST` or `PUT` requests).
    - Example: `{ "name": "John", "age": 30 }`

**Example Of An HTTP Request In Python**

```python
import requests

# Example of a GET request
response = requests.get('https://api.example.com/data')

# Example of a POST request
data = { 'name': 'John', 'age': 30 }
response = requests.post('https://api.example.com/data', json=data)

# Print the response status and data
print(response.status_code)  # Status code
print(response.json())       # Response data (for JSON response)
```
## Components Of An HTTP Response

When a server receives an HTTP request, it sends back an **HTTP response**. The response contains essential information about the result of the request, structured into the following parts:

* **Status Code** indicating whether the request was successful. These are explained below.
* **Headers** providing metadata about the response (such as content type, etc.):
	* **`Content-Type: application/json`** – Specifies the format of the response body (e.g., JSON, HTML, plain text).
	- **`Set-Cookie: sessionId=abc123`** – Sends cookies to the client for session management.
	- **`Cache-Control: max-age=3600`** – Instructs the client on how long to cache the response (e.g., 1 hour).
	- **`Content-Length: 512`** – Indicates the size of the response body in bytes.
- **Body (Optional)** containing the actual data being returned by the server:
	-  **HTML:** A webpage when requesting a URL in a browser.
	- **JSON:** Data from an API (e.g., `{"name": "John", "age": 30}`).
	- **Plain Text:** Simple text responses like "Success".

**Example HTTP Response:**
```http
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 85
Cache-Control: no-cache

{
  "status": "success",
  "data": {
    "user": {
      "name": "John Doe",
      "email": "john@example.com"
    }
  }
}
```
## HTTP Status Codes

After sending a request, the server responds with a status code that indicates the result:

- **2xx (Success):** The request was successful.
    - `200 OK`: Request succeeded.
    - `201 Created`: Resource successfully created (often after a `POST` request).
- **3xx (Redirection):** The resource has moved or requires additional actions.
    - `301 Moved Permanently`: The resource has been permanently moved to a new URL.
- **4xx (Client Error):** Something is wrong with the request.
    - `400 Bad Request`: The request is malformed.
    - `401 Unauthorized`: Authentication is required.
    - `404 Not Found`: The requested resource doesn't exist.
- **5xx (Server Error):** The server encountered an error.
    - `500 Internal Server Error`: A generic server-side error.