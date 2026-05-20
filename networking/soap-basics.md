# SOAP (Simple Object Access Protocol) Notes

SOAP is a messaging protocol used to exchange structured data between applications over a network, typically using HTTP or HTTPS. Unlike REST (which can use JSON, plain text, or XML), SOAP relies **exclusively on XML** and is:
- Highly structured and rigid.
- Used in enterprise environments, telecommunications and legacy financial systems because of its built-in standards for security and transactional reliability.

A key concept in SOAP is the **WSDL (Web Services Description Language)**. A WSDL is an XML document provided by the server that acts as a strict "contract." It tells the client exactly what functions are available, what variables they require, and what format the response will take.

## Anatomy of a SOAP Message

Every SOAP message is an XML document containing specific structural elements:

- **Envelope (Mandatory):** The root element that identifies the XML document as a SOAP message.
- **Header (Optional):** Contains application-specific information, such as authentication credentials or routing details.
- **Body (Mandatory):** Contains the actual request payload (the method being called and its parameters) or the server's response.
- **Fault (Optional):** Found inside the Body, it provides details about errors that occurred during processing.

## SOAP XML Examples

**1. The Request (Client -> Server)** Here is an example of what your application actually sends over the network to ask for notifications.

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:sch="http://playmobile.pl/notification/schema">
    <soapenv:Header>
        </soapenv:Header>
    <soapenv:Body>
        <sch:getNotificationsRequest>
            <sch:packageId>P4_Standard-20260504144900</sch:packageId>
            <sch:campaign>LEAD_2025-07-08_1</sch:campaign>
            <sch:packageSize>100</sch:packageSize>
        </sch:getNotificationsRequest>
    </soapenv:Body>
</soapenv:Envelope>
```


**2. The Response (Server -> Client)** The server replies with a similarly structured XML document containing the requested data.

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <soap:Body>
        <getNotificationsResponse>
            <notifications>
                <notification>
                    <notificationId>6036462543</notificationId>
                    <campaign>LEAD_2025-07-08_1</campaign>
                    <status>NEW</status>
                </notification>
            </notifications>
        </getNotificationsResponse>
    </soap:Body>
</soap:Envelope>
```

## Handling SOAP in Python

In Python, you generally have two ways to handle SOAP endpoints.

### 1. Modern standard with zeep library

If the web service has a WSDL file available, the `zeep` library is the absolute best way to handle SOAP in Python. 
- It parses the WSDL and creates native Python methods for you, meaning you never have to write raw XML manually. 
- It takes your Python dictionaries and translates them into XML automatically.

```python
# Required installation: pip install zeep
from zeep import Client
from zeep.transports import Transport
from requests.auth import HTTPBasicAuth
import requests

# 1. Setup authentication and transport
session = requests.Session()
session.auth = HTTPBasicAuth('username', 'password')
transport = Transport(session=session)

# 2. Initialize the client using the server's WSDL URL
wsdl_url = 'http://someinternalip:6084/outbound?wsdl'
client = Client(wsdl=wsdl_url, transport=transport)

# 3. Call the SOAP method exactly like a normal Python function
# Zeep automatically converts these arguments into the correct XML structure
response = client.service.getNotificationsRequest(
    packageId='P4_Standard-20260504144900',
    campaign='LEAD_2025-07-08_1',
    packageSize=100
)

# 4. The response is returned as a clean, accessible Python object
print(response.notifications[0].notificationId)
```

### 2. Raw HTTP Request (`requests` library)

If the WSDL is missing, broken, or you simply prefer the exact manual approach using a standard HTTP POST request and pass the raw XML string. 

You will then need an XML parser (like `xmltodict` or `xml.etree.ElementTree`) to read the response.

```python
# Required installation: pip install requests xmltodict
import requests
import xmltodict

url = "http://someinternalip:6084/outbound"
headers = {'Content-Type': 'text/xml; charset=utf-8'}
auth = ('username', 'password')

# 1. Construct the raw XML string manually
soap_body = """
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:sch="http://playmobile.pl/notification/schema">
    <soapenv:Body>
        <sch:getNotificationsRequest>
            <sch:packageId>P4_Standard-123</sch:packageId>
        </sch:getNotificationsRequest>
    </soapenv:Body>
</soapenv:Envelope>
"""

# 2. Send the POST request
response = requests.post(url, data=soap_body, headers=headers, auth=auth)

# 3. Parse the returned XML string into a Python dictionary
parsed_data = xmltodict.parse(response.text)
print(parsed_data)
```