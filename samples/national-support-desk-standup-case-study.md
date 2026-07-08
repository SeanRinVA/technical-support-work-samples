# Case Study: Standing Up a National Program Support Desk Under a Compliance Deadline

## Scenario

A federal education compliance program needed all 51 state departments of education (50 states plus the District of Columbia) to submit evidentiary documentation files proving compliance with a national K-12 accountability program. The submission and review process had no dedicated support desk in place.

I was brought in to design and launch a support desk from scratch to guide all 51 state agencies through file submission, review failing files, and get states to a passing resubmission before the compliance window closed.

This sample is sanitized and generalized. No agency names, personnel names, or internal system details are included.

---

## Starting Conditions

**Reported need:**
Fifty-one state agencies needed to submit evidentiary files against a federal compliance standard, with no standing desk to intake questions, review failures, or manage resubmission.

**Constraints:**

- Hard compliance deadline set by the federal program office.
- No existing intake process, escalation path, or documentation for reviewers to follow.
- Reviewers would be handling failing submissions from state-level staff with a wide range of technical familiarity with the file format and submission process.
- Hardware and staffing had to be stood up alongside the process itself.

---

## Design Approach

Before building a team, I had to define what "done" looked like for a single state's submission and work backward into a repeatable process:

1. Defined what a passing file submission required, so reviewers had a consistent standard rather than individual judgment calls.
2. Built an intake and triage process: how a state's submission came in, how it was logged, and how it was assigned to a reviewer.
3. Defined the failure-review workflow: what a reviewer checked first, how errors were categorized, and how findings were communicated back to state staff in language a non-technical program administrator could act on.
4. Built a resubmission loop: state staff needed a clear path from "here's what failed and why" to "here's what a corrected file needs to look like" without repeated back-and-forth cycles burning down the compliance window.
5. Planned for scale: with 51 agencies submitting on overlapping timelines, the desk needed a way to track where every agency stood without losing state-specific context between reviewers.

---

## Staffing and Operations

I built and managed a 12-person technical support desk to execute this process.

- Trained the desk on the file standard, common failure patterns, and the correction-guidance workflow.
- Managed hardware procurement and deployment for the desk's workstations and support tools.
- Assigned agencies across reviewers and rebalanced workload as submission volume shifted closer to the deadline.
- Handled escalation when a state's failure was ambiguous, politically sensitive, or outside a reviewer's judgment call — deciding when an issue needed to go back to the program office rather than be resolved at the desk level.
- Monitored desk throughput against the compliance deadline and adjusted staffing focus toward agencies at the highest risk of missing it.

---

## Findings From Running the Desk

Once the desk was operating, a few patterns became clear that shaped how the team worked:

- Most failures were not deliberate non-compliance — they were format and process misunderstandings from state staff who submitted infrequently and didn't have institutional memory of the requirements.
- Generic rejection notices did not work. Reviewers had to write correction guidance specific enough that a state employee unfamiliar with the file format could act on it without a follow-up call.
- Agencies that failed early and got clear, fast correction guidance rarely failed a second time. Agencies that got vague guidance came back with a second incomplete submission, which cost far more desk time than getting the first response right.
- Escalation criteria needed to exist before the desk opened, not get invented reactively — without them, reviewers either escalated too much (bottlenecking the program office) or too little (state agencies missing the deadline with an unresolved issue nobody flagged).

---

## Outcome

- Stood up a 12-person desk from zero to operating inside the available runway before the compliance deadline.
- Guided all 51 state agencies through the evidentiary submission process to a passing result.
- Reduced repeat-failure submissions by making first-pass correction guidance specific and actionable.
- Produced a standing process and escalation model that outlasted the initial compliance cycle.

---

## Support and Management Lessons

### 1. Define the standard before you build the team

Reviewers can't be consistent against a standard that isn't written down yet. That work has to happen before training starts, not during it.

### 2. Escalation criteria are part of the process design, not an afterthought

Deciding in advance what counts as "this needs to go up a level" keeps the desk from becoming either a bottleneck or a place where hard problems get resolved inconsistently.

### 3. Correction guidance is a communication skill, not just a technical one

The technical finding ("this field failed validation") is not the same as guidance a non-technical state employee can use. Translating between the two is most of what makes a support desk actually reduce repeat failures.

### 4. Throughput problems are staffing and workload problems, not just technical ones

Managing 51 agencies against one deadline meant actively rebalancing reviewer workload toward risk, not just processing tickets in the order they arrived.

---

## Example Interview Summary

I was asked to stand up a technical support desk from scratch to get all 51 state departments of education through a federal compliance file submission process before a hard deadline, with no existing intake, review, or escalation process in place. I built the review standard first, then designed the intake, failure-review, and resubmission workflow around it, and built and managed a 12-person desk to run it — including hardware procurement and deployment. The biggest lesson from running it was that vague rejection notices caused repeat failures; once reviewers gave state staff specific, actionable correction guidance on the first pass, most agencies didn't fail a second time. All 51 agencies reached a passing submission before the compliance window closed.
