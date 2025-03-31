# Python's email.mime and smtplib Notes

Python's `email.mime` and `smtplib` modules allow you to create and send emails programmatically.

- `email.mime`: Helps construct MIME (Multipurpose Internet Mail Extensions) messages, including text, HTML, and attachments.

- `smtplib`: Provides an interface to send emails using the Simple Mail Transfer Protocol (SMTP).

## Full Example Using Service Account With Features

```python
import smtplib  # Library for sending emails via SMTP
import logging  # Used for debugging and logging
import os  # Used for file path operations
import io  # Used for in-memory file handling
import csv  # Used for creating CSV attachments
from email.mime.text import MIMEText  # Handles plain text email content
from email.mime.multipart import MIMEMultipart  # Handles emails with multiple parts (text + attachments)
from email.mime.base import MIMEBase  # Handles file attachments
from email import encoders  # Encodes file attachments
from datetime import datetime  # Used for timestamping email subjects

# Function to send an email with optional attachments
def send_email(email_to: list, email_cc: list, custom_message: str, attachment: str = None, csv_content: list[list] = None, 
               csv_filename: str = "data.csv") -> None:
    """
    Sends an email with an optional text body, file attachment, and in-memory CSV attachment.
    
    Parameters:
    - email_to: List of recipient email addresses.
    - email_cc: List of CC email addresses.
    - custom_message: Optional email body text.
    - attachment: Optional file path to attach.
    - csv_content: Optional list of lists to attach as a CSV file.
    - csv_filename: Filename for the in-memory CSV.
    """
    
    # SMTP configuration (Replace with actual SMTP server details)
    smtp_server = "smtp.example.com"
    smtp_port = 587
    smtp_sender = "sender@example.com"
    smtp_password = "your_password"  # Use environment variables for security
    
    # Clean and format recipient email lists
    smtp_reciever = [address.strip() for address in email_to]
    smtp_cc = [address.strip() for address in email_cc]
    
    # Define email subject with timestamp
    subject = f"Notification - {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}"
    
    # Set default message body if none is provided
    body = custom_message
    
    # Create a MIMEMultipart email to support attachments
    message = MIMEMultipart()
    message["Subject"] = subject
    message["From"] = smtp_sender
    message["To"] = ", ".join(smtp_reciever)
    message["CC"] = ", ".join(smtp_cc)
    
    # Attach the email body as plain text
    message.attach(MIMEText(body, "plain"))
    
    # Attach a file from disk if provided
    if attachment and os.path.exists(attachment):
        with open(attachment, "rb") as file_attachment:
	        # Set attachment type
            part = MIMEBase("application", "octet-stream")  
            # Read file contents
            part.set_payload(file_attachment.read())  
        # Encode file in base64 for email compatibility
        encoders.encode_base64(part)  
        # Set file name
        part.add_header("Content-Disposition", f"attachment; filename={os.path.basename(attachment)}")  
        # Attach file to email
        message.attach(part)  
    
    # Attach an in-memory CSV file if provided
    if csv_content:
	    # Create an in-memory string buffer
        csv_buffer = io.StringIO()  
        # Create a CSV writer
        csv_writer = csv.writer(csv_buffer)  
        # Write the list of lists to CSV
        csv_writer.writerows(csv_content)  
        # Move to start of the buffer
        csv_buffer.seek(0)  
	     
	    # Set MIME type
        part = MIMEBase("application", "octet-stream")
        # Convert string to bytes
        part.set_payload(csv_buffer.getvalue().encode("utf-8")) 
        # Encode to base64 
        encoders.encode_base64(part)  
         # Set attachment name
        part.add_header("Content-Disposition", f"attachment; filename={csv_filename}") 
        # Attach to email
        message.attach(part)  
    
    # Send the email via SMTP
    try:
        with smtplib.SMTP(smtp_server, smtp_port) as smtp_connection:
	        # Upgrade connection to secure TLS
            smtp_connection.starttls()  
            # Log in to SMTP server
            smtp_connection.login(smtp_sender, smtp_password)   
            # Send email
            smtp_connection.send_message(message)  
            logging.info("Email sent successfully")
    except Exception as e:
        logging.error(f"Email sending failed: {str(e)}")
```

## Full Example Using Gmail Account

To be able to use your private Gmail account for sending email from withing apps, few steps needs to be taken:

1. Enable Two-Step verification on your Gmail account (without it, next step is not possible.)
2. Create an "App Password" to be used by your application
   * Settings > Security settings > App Passwords
   * Select "Mail" as the app and "Other" as the device
   * Give it meaningful name and generate password
   * Username will be your Gmail account, password your newly generated password

 
```python
import pandas as pd
import smtplib
from email.mime.text import MIMEText

def send_email(email_to: list, email_cc: list, email_subject: str, email_message: str):

    # Basic validation
    if not (isinstance(email_to, list) or isinstance(email_cc, list)) :

        raise TypeError("Make sure email_to and email_cc are lists.")

    # Configure smtp params
    smtp_user: str = "your-account@gmail.com"
    smtp_password: str = "password for your gmail app"
    smtp_server: str = "smtp.gmail.com"
    smtp_port: int = 465
   
	# Configure email params
    subject:str = email_subject
    body: str = email_message
    sender: str = smtp_user
    recipients_to: list = email_to
    recipients_cc: list = email_cc

    # Construct message
    em_message: MIMEText = MIMEText(body)
    em_message["Subject"] = subject
    em_message["From"] = sender
    em_message["To"] = ",".join(recipients_to)
    em_message["CC"] = ",".join(recipients_cc)

    # Try sending
    with smtplib.SMTP_SSL(smtp_server, smtp_port) as smtp:

        try:
            smtp.login(smtp_user, smtp_password)
            print(f"Login to {smtp_user} successfull.")
            smtp.send_message(em_message)
            print(f"Email sent successfully.")
            return True

        except Exception as e:
            print(f"Unable to send email, error: {e}")
            return False
```


## Advanced Usage

### Sending Multiple Attachments

```python
# To attach multiple files, loop through a list of file paths
attachments = ["file1.txt", "file2.pdf"]
for attachment in attachments:
    if os.path.exists(attachment):
        with open(attachment, "rb") as file_attachment:
            part = MIMEBase("application", "octet-stream")
            part.set_payload(file_attachment.read())
        encoders.encode_base64(part)
        part.add_header("Content-Disposition", f"attachment; filename={os.path.basename(attachment)}")
        message.attach(part)
```

### Sending HTML Emails

```python
html_body = """
<html>
<head></head>
<body>
    <h2>Email Notification</h2>
    <p>This is an <strong>HTML-formatted</strong> email.</p>
</body>
</html>
"""
message.attach(MIMEText(html_body, "html"))
```

## Testing Options

To preview message without sending it, here are some options.

```python
# Function to test email message formatting

def test_email():
    email_message = MIMEMultipart()
    email_message.attach(MIMEText("This is a test email.", "plain"))
    
    # Print email content as a string
    print(email_message.as_string())
    
    # Extract and decode the body
    for part in email_message.walk():
        if part.get_content_type() == "text/plain":
            print(part.get_payload(decode=True).decode("utf-8"))
    
    # Save email as a file
    with open("test_email.eml", "w", encoding="utf-8") as f:
        f.write(email_message.as_string())

# Run test function
test_email()
```


## Best Practices

1. **Use Environment Variables for Credentials**: Never store passwords in your script; use `os.environ` or a secure credential store.
    
2. **Use** `**starttls()**` **for Secure Connections**: Always encrypt email communication with TLS.
    
3. **Validate and Sanitize Email Addresses**: Ensure proper email formatting before sending.
    
4. **Check if Attachments Exist**: Prevent errors by verifying file paths before attaching.
    
5. **Use Logging for Debugging**: Helps track issues during email transmission.
    
6. **Test with** `**message.as_string()**` **Before Sending**: Debug email content before actually sending it.

