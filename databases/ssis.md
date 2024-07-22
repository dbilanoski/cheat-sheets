# Microsoft SQL Server Integration Services
Enterprise level data integration and transformation platform able to perform [ETL](https://en.wikipedia.org/wiki/Extract,_transform,_load) tasks. It automates extracting and transforming the data from one place to another.

## ETL (Extract, Transform, Load)
Three-phase process where data is [_extracted_](https://en.wikipedia.org/wiki/Data_extraction "Data extraction") from an input source, [_transformed_](https://en.wikipedia.org/wiki/Data_transformation "Data transformation") (including [cleaning](https://en.wikipedia.org/wiki/Data_cleaning "Data cleaning")), and [_loaded_](https://en.wikipedia.org/wiki/Data_loading "Data loading") into an output data container.

**Extract**
* Pull data from one or many sources.
* SQL Server, DB2, Oracle, Flat files, Excel spreadsheets and cloud apps are all valid sources.

**Transform**
* Apply business rules to the data.
* Clean up data, do aggregation, merge, math functions etc.

**Load**
* Send transformed data to a destination.

## Installation

1. Install Visual Studio
	1. [Download](https://learn.microsoft.com/en-us/visualstudio/install/install-visual-studio?preserve-view=true&view=vs-2022) Visual Studio installer and make sure to select "Data storage and processing" workload during installation.
	2. If you already have Visual Studio, re-launch the installer and just add "Data storage and processing".
2. Install SQL Server Integration Services Projects 2022 (at the moment of writing)
	1. Once in the Visual Studio, Click "Extensions", find "SQL Server Integration Services Projects" and install it.

Visit [SQL Server Data Tools](https://learn.microsoft.com/en-us/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-ver16) for updated links and more details.


## Components & Terminology

SSIS project, when edited in VS, is structured as:
* Solution, which contains the project
	* Project, which contains the packages
		* Package, which holds the processing logic.

| Item       | Description                                                                                                                                                                                                                           |
|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Solution   | Logical grouping of related projects.                                                                                                                                                                                                 |
| Project    | Collection of defined packages holding logic of the ETL task which was being handled. Project is deployable unit - once you are done defining tasks in packages, project gets deployed on the SQL server for execution or scheduling. |
| Package    | Collection of operations or tasks in a project, central unit of the SSIS.                                                                                                                                                             |
| Task       | Single operation of the SSIS package.                                                                                                                                                                                                 |
| Component  | Part of the data pipe line - can be a source, destination or transformation in the data pipe line.                                                                                                                                    |
| Execution  | Act of running the logic defined in the SSIS package.                                                                                                                                                                                 |
| Deployment | Act of deploying completed SSIS project on an instance of the SQL server where it can be executed or sheduled.                                                                                                                        |
## Package Design Structure

Consists of Control Flow, Data Flow, Parameters and Event Hanlders.

### Control Flow
* Controls the flow of execution.
* Contains different tasks, show them graphically as chained blocks of logical units describing the procedure.

### Data Flow
* One of the tasks in Control Flow - defines source, transformation or destination tasks as a single "container" of data flow tasks.


## Defining Connections
### Flat File Connection
* Browse to a flat file (text, csv, Excel)
* Once loaded, check if encoding and delimiter is fine. "Columns" should show correct tabular representation of your data.

### Local Microsoft SQL Server
Few thing to note: 
* For these connections one connection targets one specific database (not a table).
* Tables can be created by the SSIS package task, but databases cannot.
	* Once destination connection is configured in the package, table will be created (but not with data). Data will be transferred on the execution of the task.
* If need to work with different databases on the same server, each will need a separate connection.

**Steps to configure connection:**
1. Choose "ADO.NET" from the connection options in the Connection Manager.
	* [ADO.NET](https://en.wikipedia.org/wiki/ADO.NET) is a data access technology to establish communication between relational and non-relational systems .
2. For server name, type `.` (this means localhost)
3. Configure authentication (mine was "Windows Authentication").
4. Select database to work with.


## Data Transformation Tasks

### Data Conversion
* Takes input columns, performs data conversion and stores values into new columns.

**Steps to configure**
1. Once inside the data conversion block, select input columns you want to convert.
2. Rename "Output Alias" (new columns where the data will be stored) to something meaningful.
3. Chose new Data type (the one matching destination).
4. Optionally, length, precision, scale and code page can be configured.

Once in the destination mapping, make sure to select newly created columns (ones from "Output Alias").


### Derived Column
* Creates new column and allows expressions to be used where other columns can be used to produce new values.
* Example is concatenating first letters of first name and last name columns to create initials.

**Steps to configure**
1. Once inside the derived column block, there will be a section with variables and columns on the left and functions on the right. Those are all building blocks which can be used to create derived column.
2. Write "Derived Column Name", best to match column in the destination.
3. Under "Derived Column" chose either to create new column, or replace existing one on the fly.
4. Under expression, write an SQL like expression to create your new column. Functions, variables and columns from the upper sections can be used here.
5. Data type, length, precision, scale and code page can also be configured here.

### Character Map
* Provides means of changing characters by applying string function to them, such as making upper or lower case.

**Steps to configure**
1. Once inside the Character Map block, select input columns you want to work with.
2. In the column list, set "Destination" as an in-place change or a new column.
3. In the column list, set "Operation" to perform a certain string function.
	1. Useful ones will be Uppercase and Lowercase.

More details in the official docs [here](https://learn.microsoft.com/en-us/sql/integration-services/data-flow/transformations/character-map-transformation?view=sql-server-ver16).

### Sort
* Provides means of sorting the data based on a column.

**Steps to configure**
1. Once inside the Sort block, select input columns you want to work with.
2. Choose "Sort Type" as ascending or descending.


### Multicast
* Provides means of creating multiple output streams of single input making saving transformed data at multiple places at once possible.

**Steps to configure**
1. Add Multicast block and point connections to multiple destinations.


### Conditional Split
* Provides means of defining filters to further process the dataset (same as with `WHERE` statement in SQL)
* Goal is to filter data on some criteria and passing the filtered result down the pipeline.

**Steps to configure**
1. Once inside the Conditional Split block, Similar to "Derived Column", there will be a section with variables and columns on the left and functions on the right. Those are all building blocks which can be used to create filter.
2. Choose a column from "Columns" which you'll be using for the filtering
3.  In the "Condition", write your statement or choose one of the functions from the selection above.
4. If you want to give this filter a custom name, under "Output Name" rename "Case 1" to something else.
5. Once you connect this to a next step in the pipeline, it will as which filter to use, select your filter or "Case 1" if you did not rename it.