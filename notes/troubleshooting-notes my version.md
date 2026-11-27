# Troubleshooting Notes 
## 1. Timestamp Parsing Failure

### Issue

The initial timestamp comparison encountered a parsing problem.

The timestamp values in the exported CSV used this format:

`dd-MM-yyyy HH:mm:ss`

A direct DateTime conversion did not correctly interpret the exported timestamp strings.

### Cause

The exported timestamp format did not match the parsing assumptions used by the initial comparison.

The initial comparison output was therefore not reliable.

### Resolution

The parsing approach was corrected using ParseExact and invariant culture.

The expected format was explicitly defined as:

`dd-MM-yyyy HH:mm:ss`

### Validation

After correcting the parsing logic:

- All four controlled artifacts were parsed.
- CreationTime was compared with LastWriteTime.
- CreationTime was compared with LastAccessTime.
- The calculated differences were zero seconds.

### Investigation Note

The initial invalid output was excluded from the final interpretation.

The corrected output was used for the final timestamp assessment.

## 2. PowerShell CSV Export Warning

### Issue

PowerShell displayed a warning that the `-NoTypeInformation` parameter was obsolete.

### Cause

The parameter is no longer required in the same way in the PowerShell version used during this lab.

### Resolution

The warning was treated as nonblocking.

The CSV export completed successfully.

### Validation

The resulting CSV file was available for subsequent review.

No evidence of a failed export was observed.

## 3. Windows Event Collection

### Observation

The investigation collected records from:

- Windows System
- Windows Security
- Sysmon Operational

The event logs were enabled and contained records at the time of collection.

### Observed Status

| Event Log | Enabled | Record Count |
|---|---|---:|
| System | True | 40,071 |
| Security | True | 2,219 |
| Sysmon Operational | True | 50,361 |

### Investigation Note

The collected records covered the configured review window.

The absence of a relevant event in the exported files would not prove that the activity never occurred.

The event collection window and available telemetry must be considered when interpreting the timeline.

## 4. Sysmon Event ID 1 Interpretation

### Observation

The investigation exported Sysmon Event ID 1 records for process creation review.

### Limitation

Event ID 1 records process creation activity.

It does not independently prove that a process created, modified, or accessed a particular file.

### Resolution

Sysmon process creation records were retained as execution-context evidence.

They were not treated as direct proof of file modification.

## 5. Wazuh Record Associated with Agent ID 000

### Issue

During Discover review, a record was observed with:

- Agent ID: 000
- Agent name: localhost.localdomain
- Index: wazuh-archives-4.x-2026.11.27
- Location: df -P

### Assessment

The record represented manager-side telemetry.

The Windows endpoint was associated with Wazuh agent ID 001.

The observed record therefore did not confirm that Lab 147 endpoint activity had been successfully correlated.

### Resolution

The record was excluded as proof of Windows endpoint activity.

The Wazuh correlation limitation was documented rather than treating the manager-side event as endpoint evidence.

### Investigation Status

Endpoint correlation remains unresolved.

## 6. Timeline Interpretation

### Observation

The consolidated timeline contained 1,436 entries from four evidence categories.

### Limitation

The timeline combines the records that were collected during the investigation.

It does not represent a complete history of endpoint activity.

### Resolution

The timeline was interpreted as a consolidated view of available evidence rather than a complete activity record.

The collection window and source limitations were retained in the investigation notes.

