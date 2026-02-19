
## Logs & Events

### Top 5 Logged Tasks & Logs Time-Span Window

Useful for:
- Seeing the logs time range for specific log group.
- Seeing actual task names along with top 5 most logged tasks and their count.

```powershell
$LogName = 'Microsoft-Windows-TaskScheduler/Operational'

try {
    # 1. Get the oldest and newest record timestamps
    $Oldest = Get-WinEvent -LogName $LogName -Oldest -MaxEvents 1
    $Newest = Get-WinEvent -LogName $LogName -MaxEvents 1

    Write-Host "--- LOG RANGE ---" -ForegroundColor Cyan
    Write-Host "Oldest Log Entry: $($Oldest.TimeCreated)"
    Write-Host "Newest Log Entry: $($Newest.TimeCreated)"
    Write-Host "-----------------"

    # 2. List the Task Names that appear most often (so we see exact spelling)
    Write-Host "`nTop 5 Task Names found in the log:" -ForegroundColor Yellow
    Get-WinEvent -LogName $LogName -MaxEvents 500 | 
        Where-Object { $_.Id -eq 200 } | 
        Group-Object { $_.Properties[0].Value } | 
        Sort-Object Count -Descending | 
        Select-Object Count, Name -First 5 | 
        Format-Table -AutoSize

} catch {
    Write-Host "Error accessing log: $($_.Exception.Message)" -ForegroundColor Red
}
```

Result:

```text
--- LOG RANGE ---
Oldest Log Entry: 02/13/2026 00:56:00
Newest Log Entry: 02/16/2026 12:31:37
-----------------

Top 5 Task Names found in the log:

Count Name                                          
----- ----                                          
   44 \some task name                        
    3 \some task name 2                 
    2 \some task name 3                           
    2 \some task name 4
    2 \some task name 5
```

