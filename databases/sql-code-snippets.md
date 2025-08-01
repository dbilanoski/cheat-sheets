
# SQL Code Snippets & Examples

1. [Microsoft SQL Server Agent Jobs](##Microsoft%20SQL%20Server%20Agent%20Jobs)
	1. [Get Human Readable List Of SQL Jobs And Their Schedules](###Get%20Human%20Readable%20List%20Of%20SQL%20Jobs%20And%20Their%20Schedules)
	2. [Get All SQL Agent Jobs Grouped By Date & Hour](###Get%20All%20SQL%20Agent%20Jobs%20Grouped%20By%20Date%20&%20Hour)
	3. [Find SQL Jobs Details Based On Job Name Keywords](###Find%20SQL%20Jobs%20Details%20Based%20On%20Job%20Name%20Keywords)


## Microsoft SQL Server Agent Jobs

### Get Human Readable List Of SQL Jobs And Their Schedules

```sql

/*
This SQL script retrieves a human-readable list of enabled SQL Server Agent jobs and their associated schedules.

Key features:

- Displays both the job name and its associated schedule name.
- Frequency Type: Identifies the general type of schedule (e.g., Daily, Weekly, Monthly).

Human-Readable Schedule Description:

- Weekly schedules decode multiple days using bitmask logic (e.g., "Every Monday, Wednesday, Friday").
- Monthly schedules show either specific days (e.g., "Day 15 of the month") or relative positions (e.g., "First Monday of the month").
- Start Time: Converts the active_start_time integer into a standard HH:MM:SS format.
- Enabled Flags: Indicates whether the job and schedule are currently enabled.


It reads from the msdb system database and joins the necessary system tables:

- sysjobs: SQL Agent jobs
- sysschedules: Job schedule definitions
- sysjobschedules: Links jobs to schedules

Only enabled jobs and schedules are included by default. 
You can remove the WHERE clause to see all jobs and schedules, regardless of status.

Author: Internet xD
*/

USE msdb;
GO

SELECT 
    j.name AS job_name,                            -- SQL Agent job name
    s.name AS schedule_name,                       -- Schedule name assigned to the job

    -- Translate freq_type to a human-readable frequency type
    CASE s.freq_type
        WHEN 1 THEN 'One Time'
        WHEN 4 THEN 'Daily'
        WHEN 8 THEN 'Weekly'
        WHEN 16 THEN 'Monthly (specific day)'
        WHEN 32 THEN 'Monthly (relative)'
        WHEN 64 THEN 'When SQL Server Agent starts'
        WHEN 128 THEN 'Start when CPUs are idle'
        ELSE 'Other'
    END AS frequency_type,

    -- Human-readable schedule description depending on freq_type
    CASE 
        -- Monthly (relative), e.g. "First Monday of the month"
        WHEN s.freq_type = 32 THEN
            CONCAT(
                CASE s.freq_relative_interval
                    WHEN 1 THEN 'First'
                    WHEN 2 THEN 'Second'
                    WHEN 4 THEN 'Third'
                    WHEN 8 THEN 'Fourth'
                    WHEN 16 THEN 'Last'
                    ELSE 'Unknown'
                END,
                ' ',
                CASE s.freq_interval
                    WHEN 1 THEN 'Sunday'
                    WHEN 2 THEN 'Monday'
                    WHEN 3 THEN 'Tuesday'
                    WHEN 4 THEN 'Wednesday'
                    WHEN 5 THEN 'Thursday'
                    WHEN 6 THEN 'Friday'
                    WHEN 7 THEN 'Saturday'
                    ELSE 'Unknown Day'
                END,
                ' of the month'
            )

        -- Monthly (specific day), e.g. "Day 15 of the month"
        WHEN s.freq_type = 16 THEN 
            CONCAT('Day ', s.freq_interval, ' of the month')

        -- Weekly: decode bitmask to list of weekdays
        WHEN s.freq_type = 8 THEN 
            'Every ' + 
            STUFF(
                CASE WHEN s.freq_interval & 1 = 1 THEN ', Sunday' ELSE '' END +
                CASE WHEN s.freq_interval & 2 = 2 THEN ', Monday' ELSE '' END +
                CASE WHEN s.freq_interval & 4 = 4 THEN ', Tuesday' ELSE '' END +
                CASE WHEN s.freq_interval & 8 = 8 THEN ', Wednesday' ELSE '' END +
                CASE WHEN s.freq_interval & 16 = 16 THEN ', Thursday' ELSE '' END +
                CASE WHEN s.freq_interval & 32 = 32 THEN ', Friday' ELSE '' END +
                CASE WHEN s.freq_interval & 64 = 64 THEN ', Saturday' ELSE '' END,
                1, 2, '' -- Remove leading comma and space
            )

        -- Daily schedule
        WHEN s.freq_type = 4 THEN 'Every day'

        -- Fallback for unhandled types
        ELSE 'See details'
    END AS schedule_description,

    -- Format start time from HHMMSS integer to HH:MM:SS string
    STUFF(STUFF(RIGHT('000000' + CAST(s.active_start_time AS VARCHAR(6)), 6), 3, 0, ':'), 6, 0, ':') AS start_time,

    j.enabled AS job_enabled,                      -- Is the job enabled?
    s.enabled AS schedule_enabled                  -- Is the schedule enabled?
FROM 
    dbo.sysjobs j                                  -- Job definitions
JOIN 
    dbo.sysjobschedules js ON j.job_id = js.job_id -- Links jobs and schedules
JOIN 
    dbo.sysschedules s ON js.schedule_id = s.schedule_id -- Schedule definitions
WHERE 
    j.enabled = 1                                   -- Only include enabled jobs
    AND s.enabled = 1                               -- Only include enabled schedules
ORDER BY 
    j.name;                                         -- Sort by job name



```


### Get All SQL Agent Jobs Grouped By Date & Hour

```sql

WITH JobStartTimes AS (
    SELECT
        j.name AS JobName,
        h.step_name AS StepName,
        h.run_time AS StepStartTime,
		h.run_date AS StepStartDate
    FROM
        msdb.dbo.sysjobs j
    INNER JOIN
        msdb.dbo.sysjobhistory h ON j.job_id = h.job_id
    WHERE
        h.step_id > 0  -- Only look at job steps, not job starts or ends
		AND h.run_date = 20250116
)
SELECT
    StepStartDate,
	StepStartTime,
    STUFF((SELECT ', ' + JobName
           FROM JobStartTimes jst
           WHERE jst.StepStartTime = jst2.StepStartTime
           FOR XML PATH('')), 1, 2, '') AS JobNames,
    STUFF((SELECT ', ' + StepName
           FROM JobStartTimes jst
           WHERE jst.StepStartTime = jst2.StepStartTime
           FOR XML PATH('')), 1, 2, '') AS StepNames
FROM
    JobStartTimes jst2
GROUP BY
    StepStartDate, StepStartTime
ORDER BY
    StepStartDate, StepStartTime;

```


### Find SQL Jobs Details Based On Job Name Keywords

```sql
-- Declare variables for the search terms
DECLARE @Keyword1 NVARCHAR(100) = 'building center';
DECLARE @Keyword2 NVARCHAR(100) = 'buildingcenter';
DECLARE @Keyword3 NVARCHAR(100) = 'bc';
DECLARE @Keyword4 NVARCHAR(100) = 'building_center';

-- Query SQL Server Agent jobs using LOWER() for case-insensitive comparison
SELECT 
	@@SERVERNAME AS "Server Name",
    -- job.job_id,
    job.name AS "Job Name",
    job.enabled as "Enabled",
    job.description as "Description",
    job.date_created as "Creation date"
    --job.date_modified
FROM msdb.dbo.sysjobs AS job
WHERE 
    LOWER(job.name) LIKE '%' + @Keyword1 + '%'
    OR LOWER(job.name) LIKE '%' + @Keyword2 + '%'
    OR LOWER(job.name) LIKE '%' + @Keyword3 + '%'
	OR LOWER(job.name) LIKE '%' + @Keyword4 + '%';
```