# DFIR Lab 147 – Forensic Artifact Timestamp Validation

## Overview

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

## Investigation Objectives

- Examine Windows filesystem timestamp attributes.
- Compare CreationTime, LastWriteTime, and LastAccessTime.
- Record local and UTC timestamp representations.
- Calculate SHA256 hashes for controlled forensic artifacts.
- Identify and correct a timestamp-parsing error.
- Review relevant Windows Security, System, and Sysmon event records.
- Construct a consolidated forensic timeline.
- Examine Wazuh telemetry and identify limitations in endpoint event correlation.
- Document evidence, assumptions, and unresolved findings.

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

## Conclusion

This lab demonstrates a structured approach to Windows filesystem timestamp validation and forensic timeline reconstruction.

The investigation identified and corrected a timestamp-parsing issue, recorded SHA256 hashes for the controlled artifacts, reviewed Windows and Sysmon event records, and consolidated the collected evidence into a chronological timeline.

The findings did not establish timestamp manipulation or malicious activity. Wazuh endpoint correlation also remained unresolved and was documented as an investigation limitation.

The main takeaway is that forensic conclusions must be based on validated timestamps, preserved evidence, correctly interpreted event records, and clearly stated limitations.
