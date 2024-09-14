#sql #ssis
# SQL Server Integration Services Cookbook

## Table of Content

1. [Control Flow HowTo's](#Control%20Flow%20HowTo's)
2. [Data Flow HowTo's](#Data%20Flow%20HowTo's)
3. [Other Topics HowTo's](#Other%20Topics%20HowTo's)
	1. [Store SQL Query Result To A Variable](#Store%20SQL%20Query%20Result%20To%20A%20Variable)
	2. [Change Connection Manager Source String To A Variable](#Change%20Connection%20Manager%20Source%20String%20To%20A%20Variable)
	3. [Current Time Stamp As A Variable](#Current%20Time%20Stamp%20As%20A%20Variable)



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
