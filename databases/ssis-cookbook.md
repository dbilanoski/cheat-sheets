#sql #ssis
# SQL Server Integration Services Cookbook

## Table of Content

1. [Control Flow HowTo's](#Control%20Flow%20HowTo's)
2. [Data Flow HowTo's](#Data%20Flow%20HowTo's)
3. [Other Topics HowTo's](#Other%20Topics%20HowTo's)
	1. [Store SQL Query Result To A Variable](#Store%20SQL%20Query%20Result%20To%20A%20Variable)
	2. [Change Connection Manager Source String To A Variable](#Change%20Connection%20Manager%20Source%20String%20To%20A%20Variable)
	3. [Current Time Stamp As A Variable](#Current%20Time%20Stamp%20As%20A%20Variable)
	4. [Show Current Iterator Value In For Loop](#Show%20Current%20Iterator%20Value%20In%20For%20Loop)



## Control Flow HowTo's

## Data Flow HowTo's

## Other Topics HowTo's
### Store SQL Query Result To A Variable
Here I am storing a count of records from a customers table called "customers" to a variable called "customers_count".

1. If you don have a connection to a database configured, configure it.
2. Test your SQL statement in the database first.
	1. `SELECT count(*) AS cnt FROM [dbo].[Customers]` 
	   ```text
	   Result:
	     | cnt
	   1 | 120
	   ```
3. Create a variable called "customers_count", data type: Int32.
4. In the Control Flow window, add the "Execute SQL Task" and open it.
5. Configure parameters as follows:
	1. Result Set: Single row
	2. SQLSourceType: Direct input
	3. Connection Type: Select your connenection
	4. SQL Statement: `SELECT count(*) AS cnt FROM [dbo].[Customers]` 
	5. IsQueryStoredProcedure: False (if you are not using a stored procedure)
6. Then click "Result Set" in the left menu and configure as follows:
	1. Result Name: 0 
		1. If you are using ADO.NET connection, it has to be an index number.
		2. If you are using OLE DB connectin, it can be a name\\alias.
	2. Variable Name: `User::customers_count`
		1. You can choose previously created variable in the dropdown menu.

When this SQL task is executed, it will store the count of rows into customers_count variable.


### Change Connection Manager Source String To A Variable
Here I am changing the Flat File Connection's "File name" source to be a variable instead of the file in the filesystem.
* This is part of a bigger step where an foreach container is looping over files in a folder and saves each file's absolute path to this "filepath" variable. 
* Then this connection is used in the data flow task in the same foreach container as a source, taking the path from the variable on each iteration, making possible to load data from multiple files using foreach loop container.

1. Create a variable called "FilePath", data type: String.
2. In the Connection Managers, right click your connection and click "Properties".
3. Find "Expressions", click it, then to the right click on the button with three horizontal dots to open the Property Editor.
4. Under "Property", click on the empty row and select "ConnectionString" from the dropdown.
	1. This is the literal string used as a source.
5. For that ConnectionString's expression, click the "three dotted button" again to access the expression builder, then under "Variables and Parameters", find your variable. It will be like this in the end: `@[User::FilePath]`


This is now ready to be used. Good use case - as a source in the Foreach Container block.


### Current Time Stamp As A Variable

This was needed as a parameter in an Event Handler logic where the timestamp was written to a database using OLE DB driver to a database column with datetime2 data type.

Conversion was needed due to time incompatibility issues probably around milliseconds.

1. Create a variable called `CurrentTimeStamp` and set the Data Type: String.
2. Use following expression `(DT_WSTR, 50) (DT_DBTIMESTAMP2, 3) GETUTCDATE()` which will:
	1. Take the current timestamp in UTC time.
	2. Optional - pass it to the `(DT_DBTIMESTAMP2, 3)` to limit milliseconds to 3 digits.
	3. Pass it to the `(DT_WSTR, 50)` Type cast function to convert it to the string.
3. This can now be inserted into a database to a datetime2 column using OLE DB driver.

![](databases/assets/Pasted%20image%2020240730143053.png)


### Show Current Iterator Value In For Loop

During development, Windows message box can be used to print current iteration as show below. Just make sure not to use it in case of many iteration, then redirecting output to log might be more practical.

Steps:
1. Say you have a variable named "abs_path" created and added to foreach block with expression 0 so each iteration is stored in that variable.
2. Add script block to the foreach block.
3. In the script block, add read only variable "abs_path" so it can be accessed from the script.
4. Click edit script, then add code below:
   
   ```csharp
   using System; 
   using Microsoft.SqlServer.Dts.Runtime; 
   using System.Windows.Forms; 
   
   public void Main() { 
   // Retrieve the file path from the SSIS variable 
   string current_value = Dts.Variables["User::abs_path"].Value.ToString(); 
   // Display the file path in a message box 
   MessageBox.Show("Current iteration value: " + current_value); 
   // Indicate successful execution 
   Dts.TaskResult = (int)ScriptResults.Success; }
```

Result: each time the `Foreach Loop Container` iterates over a file, a message box will pop up displaying the current file name.



### Create Email Message Expression With Values From Variables

**Example scenario:** After file sorting has been done where files are sorted into folders by years, send an email reporting number of files and file list for each year using these email message template:

```text
Howdy!

- Number of files in 2024: {number}

  filename 1
  filename 2
  ...
  filename n
  
- Number of files in 2023: {number}
  
  filename 1
  filename 2
  ...
  filename n

Until the next run,
FileSortingWizzard 3000.
```

**In this approach, I'm using:**
* Script block to produce values and update variables with them.
* Send Email task with `MessageSource` property configured as an expression to produce the email content.


**Steps:**
1. Configure SMTP Connection Manager according to your vendor.
2. Create following variables:
	1. `2024folderPath`, string = "C:\\your-path-to-folder\2024"
	2. `2024filesCount`, int = 0
	3. `2024fileNames`, string, leave blank
	4. `2023folderPath`, string = "C:\\your-path-to-folder\2023"
	5. `2023filesCount`, int = 0
	6. `2023fileNames`, string, leave blank
3. Create **Script Task**:
	1. Under "read only variables", add folder path variables
	2. Under "read write variables", add file count and file names variables
	3. Click "Edit script", then write code as follows:
	   
	   ```c#
	public void Main()
	{
	    // Get paths to directories from user variables
	    string folder2024Path = Dts.Variables["User::2024folderPath"].Value.ToString();
	    string folder2023Path = Dts.Variables["User::2023folderPath"].Value.ToString();
	
	    // Setup file count and filename variables
	    int count2024 = 0;
	    string fileNames2024 = "";
	    int count2023 = 0;
	    string fileNames2023 = "";
	
	    // 2024 Files
	    // Get all files from 2024 folder
	    string[] files2024 = System.IO.Directory.GetFiles(folder2024Path);
	    // Get count of files in folder
	    count2024 = files2024.Length;
	    // Loop over those and save each filename with "new line" instruction to the filenames variable
	    foreach (string file in files2024)
	    {
	        fileNames2024 += System.IO.Path.GetFileName(file) + "\n";
	    }
	
	    // 2023 Files
	    //Get all files from 2023 folder
	    string[] files2023 = System.IO.Directory.GetFiles(folder2023Path);
	    // Get count of files in folder
	    count2023 = noSalesFiles.Length;
	    // Loop over those and save each filename with "new line" instruction to the filenames variable
	    foreach (string file in files2023)
	    {
	        fileNamesNoSales += System.IO.Path.GetFileName(file) + "\n";
	    }
	
	
	    // Set the values to SSIS variables
	    Dts.Variables["User::2024filesCount"].Value = count2024;
	    Dts.Variables["User::2024fileNames"].Value = fileNames2024;
	    Dts.Variables["User::2023filesCount"].Value = count2023;
	    Dts.Variables["User::2023fileNames"].Value = fileNames2023;
	
	    // Mark script success
	    Dts.TaskResult = (int)ScriptResults.Success;
	}```
	
4.  Variables should now be ready both for counts and for the file names. Create **Send Email Task** and configure as follows:
	1.  Under "General", `MessageSourceType` = Direct Input
	2. Configure other parameters for sending emails according to you needs.
	3. Click "Expressions", expand it and find `MessageSource`. Configure expression like this:
	   
	   ```c#
	"Howdy,\n\n" +
	"- Number of files in 2024: " + (DT_WSTR, 12) @[User::count2024] + "\n\n" + @[User::fileNames2024] + "\n" +
	"- Number of files in 2023: " + (DT_WSTR, 12) @[User::count2023] + "\n\n" + @[User::fileNames2023] + "\n\n" +
	"Until the next run," + "\n" +
	"FileSortingWizzard 3000."

```
	   
	5.  Where:
		1. (DT_WSTR,  12) converts variable to a string.
		2. "\n" creates a new line.
		3. "+" is concatenation operator.
		   
5. Test your script both for content and for email sending.

### Send Email From Script Task

This is needed due to different limitations, such as 256 char limit for "To" or "CC" fields in the "Send email" task.


Steps:
1. Create variables to store all needed data (those listed in "retrieve variables from ssis" below.)
2. Use this snippet to code the email sending function
   ```csharp
   /* Add these using statements at the top of the ScriptMain.cs file
   outside the ScriptMain class:
*/
using System.Net.Mail;
using System.Net;
using Microsoft.SqlServer.Dts.Runtime;
using System.IO; 
using System;

public void Main()
{
    // --- 1. Retrieve Variables from SSIS ---
    
    // Get the long list of recipients for the 'To' field
    string toList = Dts.Variables["User::email_to"].Value.ToString();
    // Get the list of recipients for the 'CC' field
    string ccList = Dts.Variables["User::email_cc"].Value.ToString();
    // Get the list of recipients for the 'BCC' field
    string bccList = Dts.Variables["User::email_bcc"].Value.ToString();
    // Get the sender's email address
    string mailFrom = Dts.Variables["User::email_from"].Value.ToString();
    // Get the email subject
    string subject = Dts.Variables["User::email_subject"].Value.ToString();
    // Get the email body content (can be HTML)
    string body = Dts.Variables["User::email_message"].Value.ToString();
    // Get the SMTP relay/gateway server address
    string smtpHost = Dts.Variables["User::email_smtp_host"].Value.ToString();
    // Get the SMTP port number (usually 25)
    int smtpPort = (int)Dts.Variables["User::email_smtp_port"].Value;
    // Get the full file path for the attachment
    string attachmentPath = Dts.Variables["User::File_Full_Email"].Value.ToString();

    try
    {
        // --- 2. Create Mail Objects ---
        
        // Create the main email message object
        MailMessage mail = new MailMessage();
        // Create the SMTP client object, passing the server host
        SmtpClient SmtpServer = new SmtpClient(smtpHost);

        // Set the sender's address
        mail.From = new MailAddress(mailFrom);
        // Set the subject line
        mail.Subject = subject;
        // Set the body content
        mail.Body = body;
        // Tell the mail client to interpret the body as HTML (optional, set to false for plain text)
        mail.IsBodyHtml = true; 

        // --- 3. Add Recipients (Procedural Workaround to bypass 256-char limit) ---
        
        // Add 'To' Recipients
        if (!string.IsNullOrEmpty(toList))
        {
            // Split the string by semicolon and ignore any empty entries
            string[] toRecipients = toList.Split(new char[] { ';' }, StringSplitOptions.RemoveEmptyEntries);
            foreach (string recipient in toRecipients)
            {
                // Add each individual address
                mail.To.Add(recipient.Trim());
            }
        }
        
        // Add 'CC' Recipients
        if (!string.IsNullOrEmpty(ccList))
        {
            // Split the string by semicolon and ignore any empty entries
            string[] ccRecipients = ccList.Split(new char[] { ';' }, StringSplitOptions.RemoveEmptyEntries);
            foreach (string recipient in ccRecipients)
            {
                // Add each individual address
                mail.CC.Add(recipient.Trim());
            }
        }

        // Add 'BCC' Recipients
        if (!string.IsNullOrEmpty(bccList))
        {
            // Split the string by semicolon and ignore any empty entries
            string[] bccRecipients = bccList.Split(new char[] { ';' }, StringSplitOptions.RemoveEmptyEntries);
            foreach (string recipient in bccRecipients)
            {
                // Add each individual address
                mail.Bcc.Add(recipient.Trim());
            }
        }


        // --- 4. Add Attachment ---
        
        // Check if the path variable has a value AND if the file physically exists on the server
        if (!string.IsNullOrEmpty(attachmentPath) && File.Exists(attachmentPath))
        {
            // Create a new Attachment object using the file path
            Attachment attachment = new Attachment(attachmentPath);
            // Add the attachment to the email's attachments collection
            mail.Attachments.Add(attachment);
        }
        else if (!string.IsNullOrEmpty(attachmentPath))
        {
             // If a path was provided but the file is missing, fire an SSIS warning event
             Dts.Events.FireWarning(0, "Script Task Attachment", 
                 "Attachment file not found at path: " + attachmentPath, 
                 string.Empty, 0);
        }

        // --- 5. Configure SMTP Client for Anonymous/Gateway Authentication ---
        
        // Set the port
        SmtpServer.Port = smtpPort;
        
        // Set to false to prevent the client from attempting Windows Authentication credentials.
        // This is necessary for anonymous gateways/relays.
        SmtpServer.UseDefaultCredentials = false; 

        // If your gateway requires SSL/TLS encryption, uncomment the line below:
        // SmtpServer.EnableSsl = true;

        // --- 6. Send the Email ---
        SmtpServer.Send(mail);

        // Set the task result to Success
        Dts.TaskResult = (int)DTSExecResult.Success;
    }
    catch (Exception ex)
    {
        // If an error occurs (e.g., SMTP connection fails, invalid recipient), log it
        Dts.Events.FireError(0, "Script Task Email Error", 
            "Failed to send mail: " + ex.Message, 
            string.Empty, 0);
        // Set the task result to Failure
        Dts.TaskResult = (int)DTSExecResult.Failure;
    }
}
   ```