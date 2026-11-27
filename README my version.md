# DFIR-Lab147-Forensic-Artifact-Timestamp-Validation
## Overview
### 1. Introduction

Filesystem timestamps are important forensic artifacts used to understand when files were created, modified, or accessed.

During a digital forensic investigation, these timestamps can help establish an initial sequence of activity and provide context for other evidence sources.

However, timestamps must be collected and interpreted carefully. Incorrect parsing, time-zone differences, incomplete event logs, and possible timestamp manipulation can affect the accuracy of a forensic timeline.

This lab examines Windows filesystem timestamps and combines them with Windows and Sysmon event records.

### 2. Windows Filesystem Timestamps

#### CreationTime

CreationTime represents the recorded time at which a file was created in its current filesystem location.

A copied or moved file may have timestamp behavior that differs from the original file's history.

#### LastWriteTime

LastWriteTime represents the recorded time when the file's content was last written.

This timestamp can help identify when the contents of a file were last modified.

#### LastAccessTime

LastAccessTime represents the recorded time of the file's last access.

Its behavior can depend on Windows configuration and filesystem behavior. It should not automatically be treated as a precise record of every file read.

### 3. Local Time and UTC

Forensic investigations frequently combine evidence from different sources.

Some sources represent timestamps in local time, while others use UTC.

Retaining both representations helps avoid incorrect chronological conclusions.

For this lab, the endpoint used India Standard Time:

`UTC+05:30`

The collected local timestamp and its UTC equivalent were retained in the metadata inventory.

### 4. Timestamp Parsing

Timestamp parsing converts a timestamp string into a DateTime value that can be compared or used in calculations.

A parsing operation must match the actual timestamp format.

For example, the exported timestamp format in this lab was:

`dd-MM-yyyy HH:mm:ss`

Using an incompatible parsing assumption can produce an error or an incorrect interpretation.

Explicit parsing with ParseExact and invariant culture helps ensure that the expected format is applied.

### 5. Timestamp Comparison

Comparing CreationTime, LastWriteTime, and LastAccessTime can identify differences between the recorded attributes.

A difference may justify further investigation, but it does not automatically establish suspicious activity.

Likewise, matching timestamps do not prove that the metadata has never been altered.

Timestamp analysis should be combined with other evidence, including:

- File hashes
- Windows event records
- Process execution records
- Application logs
- Other available forensic artifacts

### 6. SHA256 Integrity

SHA256 is a cryptographic hash function used to generate a fixed-length digest from file contents.

A hash can be used to compare file content at different points in an investigation.

If the content changes, the resulting SHA256 hash will ordinarily change as well.

Hashing supports integrity validation, but a hash alone does not establish a file's origin, creation time, or historical authenticity.

### 7. Windows Event Logs

Windows maintains event records across different channels.

This lab reviewed:

- Windows System
- Windows Security
- Sysmon Operational

These sources provide different types of information and should not be treated as interchangeable.

The event collection window must also be considered when interpreting missing or present records.

### 8. Sysmon Event ID 1

Sysmon Event ID 1 records process creation activity.

Depending on the available configuration and event details, it can provide:

- Process image path
- Process ID
- Command line
- Parent process information
- User context
- Process GUID

This information can help reconstruct process execution activity.

However, Event ID 1 does not independently prove that a process created or modified a specific file.

Additional file-system or file-event evidence is needed to establish that relationship.

### 9. Forensic Timeline Reconstruction

A forensic timeline combines timestamps from multiple evidence sources into a chronological view.

A useful timeline retains:

- Original timestamp context
- UTC representation
- Local representation
- Evidence source
- Event identifier
- Artifact reference
- Interpretation
- Confidence

The timeline must distinguish observed facts from analytical conclusions.

It should also document gaps in the collected evidence.

### 10. Wazuh Telemetry Correlation

Wazuh can collect and index endpoint security telemetry.

When reviewing events, the analyst must verify the agent identity and event source before attributing a record to a particular endpoint.

Manager-side records associated with agent ID 000 should not be interpreted as Windows endpoint activity from agent ID 001.

A successful index search alone does not prove that the expected endpoint event was collected.

### 11. Evidence Versus Interpretation

A forensic investigation should distinguish three categories.

#### Observed Evidence

Information directly present in collected files, metadata, event records, or logs.

#### Interpretation

An explanation of what the observed evidence may indicate.

#### Unknown or Unconfirmed

Information that cannot be established from the available evidence.

This distinction prevents an analyst from presenting assumptions as confirmed findings.

### 12. Conclusion

Windows filesystem timestamp analysis is useful for forensic investigation, but it must be performed with attention to timestamp formats, time zones, collection limitations, and evidence integrity.

Combining filesystem metadata with Windows and Sysmon event records can provide a more useful chronological view.

However, no single timestamp or event should be treated as conclusive proof without supporting evidence.

This lab focuses on validating Windows filesystem timestamps and examining how timestamp inconsistencies, parsing errors, and event-log limitations can affect a digital forensic investigation.

The investigation uses controlled files created under a dedicated lab directory. Their filesystem metadata was collected and compared, SHA256 hashes were recorded, and relevant Windows and Sysmon event records were exported for timeline reconstruction.

The lab also examines a timestamp-parsing issue encountered during analysis and documents the corrected parsing approach.

## Lab Environment

| Component | Details |
|---|---|
| Operating System | Windows 11 Pro |
| Hostname | DESKTOP-9MMM37V |
| PowerShell | 7.7.0 |
| Sysmon | 15.21 |
| Wazuh Agent ID | 001 |
| Wazuh Manager | Rocky Linux 9.8 |
| Wazuh Version | 4.13.1 |
| Time Zone | India Standard Time (UTC+05:30) |
| Lab Directory | C:\DFIR-Lab147 |

## Lab Objectives

### Primary Objectives

1. Understand the forensic significance of Windows filesystem timestamps.

2. Examine CreationTime, LastWriteTime, and LastAccessTime for controlled files.

3. Collect timestamp values in both local time and UTC.

4. Identify and correct timestamp-parsing errors during forensic analysis.

5. Compare filesystem timestamp attributes and calculate their differences.

6. Generate SHA256 hashes for controlled artifacts to support integrity validation.

7. Collect relevant Windows System and Security event records.

8. Review Sysmon Event ID 1 records to understand process creation activity.

9. Combine filesystem metadata and Windows event records into a consolidated forensic timeline.

10. Review Wazuh archive telemetry and verify the agent identity associated with collected records.

11. Distinguish confirmed observations from assumptions and unresolved findings.

12. Document evidence collection, analysis limitations, and investigation conclusions.

### Expected Learning Outcomes

By completing this lab, the analyst should be able to:

- Explain the forensic relevance of Windows filesystem timestamps.
- Recognize why timestamp formats must be validated before parsing.
- Compare timestamp attributes without overstating the results.
- Use SHA256 hashes as part of an evidence-integrity workflow.
- Interpret Sysmon process creation records within their actual scope.
- Construct a timeline from multiple evidence sources.
- Identify the difference between manager-side and endpoint-side Wazuh telemetry.
- Document uncertainty and avoid unsupported forensic conclusions.
- 
## Lab Structure

```text
C:\DFIR-Lab147
|
+-- baseline
+-- artifacts
+-- metadata
+-- events
+-- timeline
+-- analysis
+-- evidence
+-- logs
```

## Controlled Artifacts

The investigation used four controlled files:

- artifact-alpha.txt
- artifact-bravo.txt
- artifact-charlie.txt
- timestamp-test.txt

All four files were stored in the lab artifacts directory.

## Evidence Collection

The following evidence was collected:

| Evidence | Location |
|---|---|
| Baseline information | baseline |
| Controlled artifacts | artifacts |
| Filesystem timestamp inventory | metadata\filesystem-timestamps.csv |
| Windows System events | events\windows-system-events.csv |
| Windows Security events | events\windows-security-events.csv |
| Sysmon Event ID 1 review | events\sysmon-event1-review.csv |
| SHA256 hash inventory | evidence\artifact-hashes.csv |
| Timestamp discrepancy assessment | analysis\timestamp-discrepancy-assessment.txt |
| Consolidated forensic timeline | timeline\forensic-timeline.csv |

## Key Findings

### 1. Filesystem Timestamp Comparison

The filesystem metadata inventory recorded the following attributes:

- CreationTime
- CreationTimeUtc
- LastWriteTime
- LastWriteTimeUtc
- LastAccessTime
- LastAccessTimeUtc

The four controlled artifacts showed matching timestamp values across the three principal filesystem timestamp attributes at the collected resolution.

The collected local timestamp was:

`27-11-2026 07:47:19`

The corresponding UTC timestamp was:

`2026-11-27 02:17:19 UTC`

The calculated differences between the timestamp attributes were zero seconds for all four files.

Matching timestamps do not establish that the timestamps are historically accurate or that manipulation did not occur.

### 2. Timestamp Parsing Issue

The initial timestamp comparison encountered a parsing error because the exported timestamp strings used the format:

`dd-MM-yyyy HH:mm:ss`

A direct DateTime conversion did not correctly interpret the exported values.

The parsing logic was corrected using ParseExact and invariant culture.

The corrected comparison produced zero-second differences across the collected timestamp attributes.

The initial invalid comparison output was excluded from the final interpretation.

### 3. SHA256 Integrity

SHA256 hashes were calculated for all four controlled artifacts and recorded in:

`evidence\artifact-hashes.csv`

The hash inventory provides a reference for subsequent integrity validation.

A recorded hash establishes the file's content identity at the time of collection. It does not independently establish the file's origin or historical integrity.

### 4. Windows and Sysmon Event Review

The investigation exported records from:

- Windows System
- Windows Security
- Sysmon Operational

The Sysmon review focused on Event ID 1, which records process creation.

The collected event records were incorporated into the consolidated timeline.

Process creation telemetry provides process execution context. It does not independently prove that a particular file was created or modified by that process.

### 5. Timeline Reconstruction

The consolidated timeline contained 1,436 entries:

| Evidence Source | Entries |
|---|---:|
| Filesystem | 12 |
| Sysmon | 1,341 |
| Windows Security | 2 |
| Windows System | 81 |
| Total | 1,436 |

The resulting timeline was saved as:

`timeline\forensic-timeline.csv`

The timeline combines the collected filesystem metadata and Windows event records. It should not be interpreted as a complete record of all system activity.

### 6. Wazuh Telemetry Limitation

During Wazuh review, a record was observed with:

- Agent ID: 000
- Agent name: localhost.localdomain
- Index: wazuh-archives-4.x-2026.11.27

The record contained manager-side telemetry rather than confirmed Windows endpoint activity from agent ID 001.

Therefore, the observed record does not establish that Lab 147 endpoint activity was successfully correlated in Wazuh.

Endpoint correlation remains unresolved.

## Important Investigation Limitations

- The investigation used controlled files rather than a confirmed real-world incident artifact.
- Matching filesystem timestamps do not prove the absence of timestamp manipulation.
- The initial timestamp parsing output was invalid and was excluded.
- Sysmon Event ID 1 provides process creation information, not direct proof of file creation.
- The collected Windows event windows do not represent the complete historical activity of the endpoint.
- The Wazuh record reviewed was associated with agent ID 000, not the Windows endpoint agent ID 001.
- No malicious activity or timestamp manipulation was confirmed.

