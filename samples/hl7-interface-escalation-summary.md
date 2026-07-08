# Escalation Summary: Providers Not Receiving Inbound Lab Results During Surgery

This sample is sanitized and generalized. It reflects a recurring pattern from clinical system implementation and support work rather than a single verbatim ticket, and does not include facility names, hostnames, credentials, or production log content.

---

## Issue Summary

**Customer / Environment:**
Hospital environment running an anesthesia information management system integrated with the hospital's source systems over HL7 v2 through an interface engine.

**Issue:**
Providers in active cases were not receiving inbound lab result (LAB) messages inside the anesthesia system during surgery. Lab values that should have appeared automatically as they resulted were missing or significantly delayed while a case was in progress.

**Business Impact:**
This was a live, in-case issue, not a documentation or after-the-fact reporting problem. Providers make real-time intraoperative decisions (transfusion, electrolyte correction, and similar) off lab values as they result. Without those values flowing automatically into the anesthesia system during the case, staff had to fall back on manually calling or checking the lab directly, adding delay and risk during active surgery.

**Current Status (at time of escalation):**
Open — treated as a priority escalation given the in-case impact, with hospital IT and the health system's regional IT team engaged on the network side.

---

## Timeline

| Stage | Event |
|---|---|
| T+0 | Anesthesia staff report that lab results are not appearing in the anesthesia system during an active case. |
| T+0 to T+1 | Interface engine inbound message queue and message history reviewed; confirmed LAB messages were not arriving as expected. |
| T+1 | Application, service, and interface logs reviewed across the communications server; confirmed the lab system was still generating LAB messages. |
| T+1 to T+2 | Compared the current network access control list (ACL) governing traffic to the interface engine against the known-good ACL baseline captured at go-live. |
| T+2 | Identified that a recent ACL update, pushed by the health system's regional IT team, had stripped the specific lines permitting the lab system's traffic to the interface engine. |
| T+2 to T+3 | Provided hospital IT the exact lines that needed to be restored; hospital IT coordinated directly with regional IT to apply the fix. |
| T+3 | Inbound LAB message flow validated across a full day of case volume before closing the escalation. |

---

## What Was Reported

During active surgical cases, lab result (LAB) messages that normally populated automatically inside the anesthesia system were missing or arriving well after the fact. Providers noticed they weren't seeing lab values update during a case the way they expected, forcing manual follow-up with the lab mid-procedure.

---

## What Was Checked

- Interface engine inbound message queue status and message history, filtered to LAB message traffic specifically.
- Whether the hospital's lab system was still transmitting LAB messages at all (ruling out a lab-side outage versus a delivery problem).
- Application, service, and interface logs on the communications server for connection resets or failed inbound transmissions.
- The current network ACL governing traffic to and from the interface engine, compared line by line against the known-good ACL baseline documented at go-live.
- Whether the ACL change correlated with a recent network policy push from the health system's regional IT team, separate from the local hospital's own IT group.

---

## Findings

The lab system was still generating LAB messages correctly, and the interface engine's inbound listener was configured and behaving as expected — this ruled out both a lab-side outage and an interface-side defect early. Comparing the live ACL against the pre-go-live known-good baseline showed the actual cause: a recent ACL update pushed by the regional IT team (which managed network policy across multiple facilities, not just this site) had stripped the specific lines permitting the lab system's traffic to reach the interface engine. The change was not targeted at this interface at all — it was a broader regional policy push that had an unintended side effect on this site's clinical traffic.

---

## Probable Root Cause

A regional network ACL update removed the specific access lines required for the lab system's HL7 traffic to reach the interface engine. This was a network policy change made outside the site's own change process, not a defect in interface configuration, message handling, or local hospital IT's network management.

---

## Remediation Performed

- Ruled out a lab-side outage and an interface-side defect early by confirming both were behaving correctly, which focused the investigation on the network layer.
- Pulled the known-good ACL baseline documented at go-live and compared it line by line against the ACL currently in effect.
- Identified the exact lines missing from the current ACL relative to the baseline.
- Provided hospital IT the specific lines that needed to be restored, rather than a general "check connectivity" request.
- Hospital IT coordinated directly with the regional IT team to get the corrected ACL applied, since regional IT owned that network layer.
- Monitored inbound LAB message flow once the ACL was corrected, confirming messages began arriving reliably.
- Documented the failure pattern for the implementation team's shared site documentation: when LAB delivery fails after being stable since go-live, compare the live ACL against the go-live baseline before assuming an interface-side cause.

---

## Validation

Tracked inbound LAB message flow across a full day of normal case volume after the ACL fix, confirming lab values populated correctly and promptly in the anesthesia system during live cases, with no further connection issues in the logs.

---

## Remaining Risks

- The ACL is owned and managed by regional IT, not by hospital IT or the implementation team directly; a future regional policy push could reintroduce the same class of issue without warning to the site.
- Because this class of failure directly affects intraoperative decision-making, any recurrence needs to be triaged as a priority, not a standard queue item.

---

## Recommended Follow-Up

- Keep the go-live ACL baseline on file and treat it as the reference point for any future connectivity investigation, so a comparison like this one can happen quickly.
- Recommend hospital IT ask regional IT to include this site's clinical interface traffic in the change-review process for future network-wide ACL or policy updates, so a broad push doesn't silently break a live clinical interface again.
- Flag LAB message delivery specifically as a high-priority monitoring point given its direct link to intraoperative care, distinct from lower-urgency data like scheduling updates.
