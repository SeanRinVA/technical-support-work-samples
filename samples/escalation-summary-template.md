# Escalation Summary Template

## Issue Summary

**Customer / Environment:**  
Sanitized customer or environment name

**Issue:**  
Brief description of the reported problem.

**Business Impact:**  
Describe what is affected, who is affected, and whether the issue is blocking production work.

**Current Status:**  
Open / Monitoring / Remediated / Awaiting validation

---

## Timeline

| Time | Event |
|---|---|
| HH:MM | Ticket opened |
| HH:MM | Initial validation performed |
| HH:MM | Backup and tape records reviewed |
| HH:MM | Server/media coordination issue identified |
| HH:MM | Remediation applied |
| HH:MM | Backup and restore validation completed |

---

## What Was Reported

Summarize the customer-reported symptom in plain language.

Example:

A restore was requested for data from a Unix/Solaris-based system. The expected restore point was not available or did not recover the requested data.

---

## What Was Checked

- Backup job history
- TSM server roles
- Tape library activity
- Media ownership records
- Server-to-library communication pattern
- Retention expectations
- Available restore points
- Restore command syntax
- Backup and restore validation results

---

## Findings

Summarize what was discovered.

Example:

The backup process appeared to complete, but the available restore data did not match recovery expectations. Review showed that multiple TSM servers were writing to the same IBM tape library without reliable shared media coordination, creating conditions where one server could overwrite media written by another server.

---

## Probable Root Cause

State the most likely cause based on evidence.

Example:

The probable root cause was server-side backup infrastructure design. Multiple TSM servers had simultaneous access to the tape library without a reliable controlling/logging layer for media ownership and access coordination.

---

## Remediation Performed

- Reviewed the TSM server and tape library architecture.
- Identified the media overwrite risk caused by uncoordinated server access.
- Rebuilt the backup infrastructure around a controlled media-access model.
- Preserved distributed backup collection from source systems.
- Added a controlling/logging server role for media access coordination.
- Ran validation backups.
- Performed test restores.
- Documented the corrected architecture and restore process.

---

## Validation

Describe how the fix was proven.

Example:

Backup jobs completed successfully after the redesign, media access was coordinated through the defined control/logging point, and test restores confirmed that expected data could be recovered.

---

## Remaining Risks

- Older media may still reflect prior coordination gaps.
- Continued periodic restore testing is recommended.
- Documentation should be reviewed after future server, tape library, or media pool changes.

---

## Recommended Follow-Up

- Schedule recurring restore validation.
- Review backup server roles and media ownership periodically.
- Maintain the backup and restore runbook.
- Confirm ownership for future configuration changes.
