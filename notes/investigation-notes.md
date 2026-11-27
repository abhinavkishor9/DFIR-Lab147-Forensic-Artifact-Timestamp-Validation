# Investigation Notes – DFIR Lab 147

## 1. Investigation Summary

The objective of this investigation was to examine Windows filesystem timestamps, validate timestamp consistency across controlled artifacts, preserve file integrity through SHA256 hashing, and reconstruct a timeline using filesystem metadata and Windows event records.

The investigation was conducted on the Windows endpoint DESKTOP-9MMM37V.

The analysis also included a review of Wazuh telemetry to determine whether endpoint activity could be correlated with the collected Windows evidence.

## 2. Investigation Environment

| Parameter | Value |
|---|---|
| Hostname | DESKTOP-9MMM37V |
| Operating System | Windows 11 Pro |
| PowerShell | 7.7.0 |
| Sysmon | 15.21 |
| Wazuh Agent ID | 001 |
| Wazuh Manager | Rocky Linux 9.8 |
| Wazuh Version | 4.13.1 |
| Time Zone | India Standard Time |
| Lab Directory | C:\DFIR-Lab147 |

## 3. Baseline Collection

The baseline information was collected before the artifact review.

Baseline evidence included:

- Hostname
- Operating system information
- PowerShell version
- Collection timestamp
- Local timestamp
- UTC timestamp

Recorded baseline time:

- UTC: 2026-11-27 02:16:21.671
- Local: 2026-11-27 07:46:21.680

The baseline provides an environmental reference for interpreting the subsequent evidence.

## 4. Controlled Artifact Collection

Four controlled files were examined:

| Artifact | Location |
|---|---|
| artifact-alpha.txt | C:\DFIR-Lab147\artifacts |
| artifact-bravo.txt | C:\DFIR-Lab147\artifacts |
| artifact-charlie.txt | C:\DFIR-Lab147\artifacts |
| timestamp-test.txt | C:\DFIR-Lab147\artifacts |

The files were used to examine filesystem metadata and validate timestamp collection.

## 5. Filesystem Timestamp Analysis

The filesystem timestamp inventory was saved as:

`metadata\filesystem-timestamps.csv`

The collected attributes included:

- CreationTime
- CreationTimeUtc
- LastWriteTime
- LastWriteTimeUtc
- LastAccessTime
- LastAccessTimeUtc

The collected timestamp values for all four files were:

- Local: 27-11-2026 07:47:19
- UTC: 2026-11-27 02:17:19

The three principal timestamp attributes matched at the collected resolution.

The calculated differences were:

| Artifact | Timestamp Difference |
|---|---:|
| artifact-alpha.txt | 0 seconds |
| artifact-bravo.txt | 0 seconds |
| artifact-charlie.txt | 0 seconds |
| timestamp-test.txt | 0 seconds |

### Interpretation

The collected metadata did not show a difference between the three timestamp attributes for the four controlled files.

This is a limited observation about the metadata collected during this lab.

It does not prove that the timestamps were never modified or that the files have no earlier history.

## 6. Timestamp Parsing Issue

During the initial comparison, a timestamp-parsing error occurred.

The exported timestamp strings followed this format:

`dd-MM-yyyy HH:mm:ss`

A direct DateTime conversion did not correctly interpret the values.

The initial comparison output was therefore not considered reliable and was excluded from the final analysis.

### Corrected Parsing Approach

The timestamp strings were parsed using ParseExact with invariant culture.

The corrected approach used the following format:

`dd-MM-yyyy HH:mm:ss`

This allowed the exported values to be interpreted according to their actual format.

### Result

After correcting the parsing logic, the timestamp comparison returned zero-second differences for all four controlled artifacts.

The corrected output was used for the final assessment.

## 7. SHA256 Integrity Verification

SHA256 hashes were collected and saved in:

`evidence\artifact-hashes.csv`

| Artifact | SHA256 |
|---|---|
| artifact-alpha.txt | 1F4565810264BCA229F7D91D8A9FB4467A5C041E8BE35DD4047288ECEAE014BF |
| artifact-bravo.txt | 15CAAF7759254B2BAC8E998E0B4A2EEE164E84E0642AC3D2ED1BCE8B5F4D7D7D |
| artifact-charlie.txt | 781D48A850816FF95D90761EB36C0F773979B734E9D92805A9038F3585B23A66 |
| timestamp-test.txt | A7D42A6F2152741B8E3452E645C77305CA1D196FC016BD1AF98FE170DCB3E872 |

The hash inventory provides a reference for future integrity comparisons.

A hash value alone does not establish the historical origin or authenticity of a file.

## 8. Windows Event Collection

The investigation exported event records from three Windows event sources.

### Windows System

Output:

`events\windows-system-events.csv`

The collection covered the preceding two-hour review window.

### Windows Security

Output:

`events\windows-security-events.csv`

The collection covered the preceding two-hour review window.

### Sysmon Event ID 1

Output:

`events\sysmon-event1-review.csv`

The review focused on process creation events recorded during the preceding two-hour window.

Sysmon Event ID 1 provides process execution information, including available process and parent-process context.

It does not independently establish that a process created or modified a particular file.

## 9. Event Log Status

The following event log status information was observed during collection:

| Event Log | Enabled | Record Count |
|---|---|---:|
| System | True | 40,071 |
| Security | True | 2,219 |
| Sysmon Operational | True | 50,361 |

These values represent the observed log status and record counts at the time of collection.

## 10. Timeline Reconstruction

The consolidated timeline was saved as:

`timeline\forensic-timeline.csv`

The timeline contained 1,436 entries.

| Source | Entries |
|---|---:|
| Filesystem | 12 |
| Sysmon | 1,341 |
| Windows Security | 2 |
| Windows System | 81 |
| Total | 1,436 |

The timeline included:

- TimestampUTC
- TimestampLocal
- EvidenceSource
- Artifact
- EventDescription
- EventID
- RecordID
- Process
- User
- EvidenceReference
- Interpretation
- Confidence

The earliest displayed events were recorded at:

- UTC: 2026-11-27 01:04:46
- Local: 2026-11-27 06:34:46

The timeline combines the evidence sources collected for this lab.

It does not represent every activity that occurred on the endpoint.

## 11. Wazuh Review

The Wazuh review included examination of the archive index:

`wazuh-archives-4.x-*`

A record was observed with the following details:

| Field | Value |
|---|---|
| Timestamp | Nov 27, 2026 08:13:24.026 |
| Index | wazuh-archives-4.x-2026.11.27 |
| Agent ID | 000 |
| Agent Name | localhost.localdomain |
| Decoder | ossec |
| Location | df -P |

The record contained manager-side telemetry.

It was not a confirmed Windows endpoint event from agent ID 001.

### Assessment

The observed record cannot be used as proof of successful Lab 147 endpoint event correlation.

Wazuh correlation for the Windows endpoint remains unresolved.

## 12. Evidence-Based Assessment

### Confirmed

- Four controlled artifacts were examined.
- Filesystem timestamp metadata was collected.
- The initial timestamp-parsing issue was identified.
- The parsing approach was corrected.
- The corrected timestamp comparison returned zero-second differences.
- SHA256 hashes were recorded for all four artifacts.
- Windows System, Security, and Sysmon event records were exported.
- A consolidated timeline containing 1,436 entries was created.
- The Wazuh record reviewed belonged to agent ID 000.

### Not Established

- Historical timestamp manipulation.
- Malicious file creation.
- Malicious process execution.
- A confirmed relationship between a Sysmon process creation event and a specific artifact modification.
- Successful Wazuh correlation of Lab 147 endpoint activity through agent ID 001.

## 13. Final Assessment

The collected evidence supports a limited validation of filesystem timestamps for the four controlled artifacts.

The parsing issue was corrected before the final comparison was interpreted.

The Windows and Sysmon records were incorporated into a consolidated timeline, but the available evidence does not establish malicious activity or timestamp manipulation.

The Wazuh record reviewed was manager-side telemetry and did not confirm endpoint correlation.

The investigation therefore concludes with validated metadata collection and timeline reconstruction, while retaining the identified telemetry and historical-evidence limitations.
