# Case Study: Owning the Technical Implementation of a Multi-Site Clinical Software Deployment

## Scenario

A clinical software vendor sold a standardized product deployment to hospitals: a database tier, an application tier, a communications/interface tier, and a set of clinical workstations, installed and brought live inside a customer-owned hospital environment. My role was technical implementation lead for these deployments — responsible for the technical delivery from the point sales handed it off through go-live and turnover to support. A project manager owned the overall project (scope, schedule, contract modifications); my ownership was the technical build and its execution across every phase, working alongside that PM rather than in that role.

Every site had the same basic phase structure, whether the customer was a commercial hospital, a VA medical center, or a DoD military treatment facility, with adjustments for each environment's specific requirements. This sample describes that phase structure and the coordination work it required — it is sanitized and generalized, and does not name a facility, vendor, product, or individual.

For the underlying storage/availability architecture and a representative production troubleshooting incident from this same work, see the companion samples in this repository: `storage-and-ha-design-case-study.md` and `hl7-interface-escalation-summary.md`.

---

## Starting Conditions

**Handoff point:**
A signed order specifying a tentative clinical workstation count and site scope, handed from sales to the implementation team.

**Constraints:**

- The customer's IT organization frequently treated the product as "a medical device, not our circus" and delegated day-to-day coordination to biomedical engineering staff who were rarely enterprise server/application administrators.
- Facilities varied widely in scope ambition — some wanted the product in a couple of clinical spaces, others wanted it deployed across many more procedural areas than originally scoped.
- Workstations were being deployed into active clinical spaces, which constrained when physical deployment work could happen.
- New installations required a period of running the old and new systems in parallel before go-live, which added scheduling pressure on top of the technical build.
- The technical build itself depended on prerequisite work (network, directory services, device staging) that the customer's local IT had to complete before the vendor team could start — work the vendor team did not control the timeline for.

---

## Phase 1: Kickoff and Charter

A virtual kickoff call with the sales team reviewed the deal specifics and the facility's initial thinking on where the product would be installed — this ranged from a narrow footprint to a much broader one across multiple procedural areas.

An onsite charter meeting followed: the project manager gave a high-level project overview, a clinical specialist demonstrated the product, and the team walked the facility to confirm exact workstation install locations. The group then split into clinical and technical breakout sessions. In the technical session, I gave a working-level walkthrough and went into implementation detail as needed, working directly with the customer's biomedical engineering staff rather than their IT department in most cases.

---

## Phase 2: Prerequisites and Build Planning

After the charter meeting, the team reviewed notes, identified any contract modifications needed, and finalized the site plan and hardware orders. I began working directly with the customer's local technical contacts to clear prerequisites ahead of the server build: directory service organizational structure, security group creation, and device pre-staging and permissions where high availability was part of the order.

This phase ran on the customer's timeline, not the vendor team's, which meant tracking multiple sites' prerequisite status in parallel and sequencing onsite build trips around whichever sites were actually ready.

---

## Phase 3: Server Build

Once prerequisites were confirmed, I went onsite to build the environment using a PowerShell-based deployment script package, driven by a prerequisite worksheet that captured site-specific variables the scripts needed as input. When a script step failed, I diagnosed the failure, adjusted the script if the issue was site-specific, and resumed from the failed stage rather than restarting the whole build.

The build reached a domain-join checkpoint, at which point the customer's local IT/biomed staff performed the domain join (a boundary I did not cross into their directory services). After that, I completed the post-domain-join configuration, handled any steps that couldn't be scripted, and coordinated with the hospital's interface contacts to test message connectivity before considering the server build complete.

Test and Production environments were built in the same trip, which meant the build plan had to account for both from the start rather than treating Test as an afterthought.

---

## Phase 4: Clinical Configuration and Validation

While onsite for the server build, I also set up the training-room workstations the clinical specialist would use with the facility's designated "super users" to configure the product's clinical side, initially pointed at the Test environment.

Configuration issues found during that clinical training work were cleaned up remotely in the following weeks. A few weeks later, the clinical team validated the Test build directly ("test in test"), and only after that validation were workstations re-pointed to Production.

---

## Phase 5: Workstation Imaging and Deployment

On a separate, later onsite trip, I built and validated an initial clinical workstation image — confirming connectivity to both the server environment and any patient-connected devices — then used that same workstation for that trip's test-in-test pass before re-imaging it and pointing it to Production to remove any test artifacts.

Additional workstations, already onsite, were imaged from that validated image and then taken through everything *up to* domain join immediately — pre-domain-join preparation completed so the machines were as far along as they could be without creating an Active Directory computer account. They were held in onsite storage at that checkpoint rather than joined and finished. Joining the domain right after imaging would have put a computer account into AD well before final placement and go-live; if that account sat unused long enough, it could tombstone (or otherwise age out of a usable state) before the workstation was moved into the clinical space, which meant domain authentication would fail on install day and force rework. Stopping short of domain join avoided that identity lifecycle risk *and* left a shorter remaining sequence for placement night: domain join, post-domain-join PowerShell-scripted build-out (with only a handful of manual steps left after that), and validation.

Deployment itself meant moving each workstation out of onsite storage into the clinical area where it would actually be used, then completing the domain join, running the post-domain-join PowerShell-scripted build-out, and validating connectivity. This typically happened after hours, since these machines were going into active clinical spaces. For new installations (as opposed to upgrades of an existing system), the facility ran a period of parallel documentation on the old (typically paper) and new systems before cutover.

---

## Phase 6: Go-Live and Turnover

The final onsite trip performed a full checkout of every workstation — a more rigorous pass than the connectivity checks done earlier — to certify each one ready for first patient use. The following day was the first day of live clinical use.

The onsite team, myself included, stayed through go-live specifically to be available if a production issue surfaced (see the HL7 interface sample for a representative example of that kind of issue). After a few days of stable live use, any remaining open items were formally handed off to the ongoing support team at turnover, and the team headed home — my role in that specific deployment ended at that point.

---

## Cross-Environment Consistency

The same six-phase structure applied whether the customer was a commercial hospital, a VA facility, or a DoD facility, with environment-specific requirements layered on top — DoD sites required vulnerability scanning and security hardening remediation before go-live, and access arrangements varied by customer (in two commercial deployments, the customer's own IT leadership granted temporary elevated access for the duration of the build and revoked it once the build was complete).

---

## Delivery Lessons

### 1. Coordinating with non-technical stakeholders is a distinct skill from doing the technical work

Most of my day-to-day counterparts were biomedical engineers, not systems administrators. Successful delivery depended on calibrating explanations to that audience and catching gaps in their environment (network, directory services, permissions) before those gaps became build-day surprises.

### 2. Build Test and Production together, and validate Test before anyone touches Production

Running "test in test" as a real gate — not a formality — caught clinical configuration issues before they reached the live environment, at the cost of extra planning to keep both environments moving through the same phases in parallel.

### 3. Physical and identity lifecycle constraints are part of the schedule, not just IT trivia

Domain computer-account aging / tombstoning was a real deployment constraint, not a footnote. Completing pre-domain-join work right after imaging kept placement night short, while deferring the domain join itself until the workstation was in its final clinical location avoided accounts aging out before go-live. Planning that split prevented rework that would otherwise show up as a mysterious "workstation fell off the domain" problem on install day.

### 4. Presence during go-live is a risk-management decision, not busywork

Being onsite on day one with "nothing to do" most of the time was deliberate: the cost of being unavailable if something did go wrong was much higher than the cost of a quiet day onsite.

### 5. A repeatable phase model scales better than a bespoke plan per site

Applying the same six-phase structure across commercial, VA, and DoD sites — adjusting only what the environment required — made each new deployment faster to plan and reduced the chance of skipping a step that mattered.

