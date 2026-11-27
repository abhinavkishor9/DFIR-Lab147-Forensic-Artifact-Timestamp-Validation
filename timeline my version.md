# Timeline 
## 1. Timeline Overview

This document describes the construction and interpretation of the consolidated forensic timeline for Lab 147.

The timeline combines filesystem metadata and selected Windows event records collected from the endpoint.

Output file:

`timeline\forensic-timeline.csv`

## 2. Investigation Time Context

| Parameter | Value |
|---|---|
| Hostname | DESKTOP-9MMM37V |
| Local Time Zone | India Standard Time |
| UTC Offset | UTC+05:30 |
| Baseline UTC | 2026-11-27 02:16:21.671 |
| Baseline Local | 2026-11-27 07:46:21.680 |

Both UTC and local timestamps were retained to support comparison across evidence sources.

## 3. Filesystem Evidence

Four controlled artifacts were examined.

| Artifact | Local Timestamp | UTC Timestamp |
|---|---|---|
| artifact-alpha.txt | 27-11-2026 07:47:19 | 2026-11-27 02:17:19 |
| artifact-bravo.txt | 27-11-2026 07:47:19 | 2026-11-27 02:17:19 |
| artifact-charlie.txt | 27-11-2026 07:47:19 | 2026-11-27 02:17:19 |
| timestamp-test.txt | 27-11-2026 07:47:19 | 2026-11-27 02:17:19 |

The three principal filesystem timestamp attributes matched at the collected resolution for all four artifacts.

The filesystem evidence contributed 12 entries to the consolidated timeline.

## 4. Windows Event Evidence

The following sources were included:

| Evidence Source | Exported File | Entries |
|---|---|---:|
| Sysmon Event ID 1 | events\sysmon-event1-review.csv | 1,341 |
| Windows Security | events\windows-security-events.csv | 2 |
| Windows System | events\windows-system-events.csv | 81 |

The Windows event exports covered the preceding two-hour review window.

Sysmon Event ID 1 was reviewed as process execution evidence.

It was not interpreted as direct proof of file creation or modification.

## 5. Consolidated Timeline Statistics

| Evidence Source | Entries |
|---|---:|
| Filesystem | 12 |
| Sysmon | 1,341 |
| Windows Security | 2 |
| Windows System | 81 |
| Total | 1,436 |

The consolidated timeline file had a recorded size of 1,856,453 bytes.

Its recorded last-write time was:

`2026-11-27 08:01:36` local time.

## 6. Timeline Fields

The consolidated CSV included the following fields:

| Field | Purpose |
|---|---|
| TimestampUTC | Event timestamp represented in UTC |
| TimestampLocal | Local timestamp representation |
| EvidenceSource | Origin of the record |
| Artifact | Related artifact, where applicable |
| EventDescription | Description of the event or metadata |
| EventID | Windows event identifier, where available |
| RecordID | Event record identifier, where available |
| Process | Process information, where available |
| User | User information, where available |
| EvidenceReference | Reference to the collected evidence |
| Interpretation | Analytical interpretation |
| Confidence | Confidence classification |

## 7. Earliest Displayed Event

The earliest displayed event in the consolidated timeline was recorded at:

- UTC: 2026-11-27 01:04:46
- Local: 2026-11-27 06:34:46

This represents the earliest displayed record in the generated timeline, not necessarily the earliest activity on the endpoint.

