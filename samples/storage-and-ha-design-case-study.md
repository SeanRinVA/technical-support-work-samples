# Case Study: Storage Isolation and Availability Design for a Standardized Clinical Deployment

## Scenario

A clinical software vendor's standard hospital deployment package included a database tier, an application tier, and a communications/interface tier, repeated across many sites with the same base design. My role was to build each site to a repeatable, documented storage and availability standard rather than improvising the SAN and high-availability configuration at every facility.

This sample is sanitized and generalized. It describes a standard architecture pattern applied across many deployments, not one facility's configuration, and does not include hostnames, IP addressing, credentials, or facility-identifying detail.

---

## Design Constraints

- Each site's database, application, and communications roles ran as separate VMs on shared physical hosts, connected to a SAN over Fibre Channel.
- Storage needed to support both a Test and a Production instance of the deployment at every site, without test activity risking or crowding production data.
- The application tier generated case-report output files as part of normal operation. Those files needed to reach the hospital's own document/imaging system, but nothing about that workflow could be allowed to threaten database storage availability.
- Some sites ordered high availability; others did not. The design had to scale down cleanly to a single-node site and up to an HA site without changing the underlying pattern.
- The communications/interface tier carried HL7 message traffic. HL7 message processing at this tier did not support automatic failover, so any HA approach for that role had to work within a manual-failover model rather than assume seamless automatic failover was available.

---

## Storage Design

**Per-VM LUN isolation:**
Each VM (database, application, communications, and their test counterparts) was provisioned with its own Swap and Data LUNs on the SAN, mounted as direct-attached storage rather than shared cluster storage. This kept each VM's storage I/O and capacity independent of the others.

**Test/Production separation at the storage layer:**
The database VM carried additional dedicated LUNs beyond its own Swap/Data pair: separate DB, Log, and Backup LUNs for Test, and separate DB, Log, and Backup LUNs for Production. Test and Production database storage was physically separated at the LUN level rather than sharing a data store with logical separation only — a design choice made specifically so test workload or a test-side issue could not affect production database performance or capacity.

**Isolating generated files from database storage:**
The application tier wrote its case-report output to a file share on its own VM rather than the database VM. This kept a steadily growing set of output files from ever competing with the database for drive space. A scheduled cleanup task purged files older than 30 days from that share once the hospital's downstream system had confirmed ingestion, so the share stayed bounded in size without manual maintenance. The hospital's own document/imaging system pulled the file in on its own schedule and treated it as the system of record — the application tier's job was to make the file available and get out of the way, not to manage its lifecycle after that point.

---

## Availability Design

The two roles that needed availability had fundamentally different failure characteristics, so I didn't use one HA pattern for both.

**Application tier (stateless-ish, request/response):**
When a site ordered HA, the application/web tier used DNS round robin across nodes. This is a simple mechanism, not true load balancing — no health-aware traffic distribution, no automatic removal of a failed node from rotation. It was an appropriate match for the tier's traffic pattern and kept the HA design consistent with what the platform actually supported, rather than implying a load-balancing capability that wasn't there.

**Communications/interface tier (stateful message processing):**
HL7 message handling did not support automatic failover, so the HA pattern here was a live node with a "warm" standby rather than an active/active or auto-failover cluster. Failing over to the standby was a deliberate manual action, not something the environment did on its own. Documenting this distinction mattered: an administrator expecting the same automatic-failover behavior as the application tier would misjudge recovery time during an actual incident.

---

## Outcome

- Applied a consistent, documented storage and availability pattern across many site deployments instead of a bespoke design per site.
- Test and Production database storage was isolated at the LUN level, removing a class of resource-contention and blast-radius risk between environments.
- Case-report output was isolated from database storage by design, with an automated retention job preventing unbounded file growth.
- Availability design matched the actual failure and recovery behavior of each tier rather than assuming one HA pattern fit both, which kept operational expectations accurate during real incidents.

---

## Design Lessons

### 1. Match the availability mechanism to what the component actually supports

DNS round robin is not load balancing, and a warm standby is not automatic failover. Using accurate language internally and in documentation prevents a false sense of resilience that only gets discovered during an actual failure.

### 2. Physical storage separation is sometimes worth the overhead

Logical separation within shared storage is often good enough, but where test activity could plausibly degrade production performance or capacity, separating at the LUN level removed the risk instead of managing around it.

### 3. Design output file lifecycle, don't just design for the happy path

A file share that only grows is a future incident. Isolating it from critical storage and pairing it with an automatic retention policy meant the design didn't depend on someone remembering to clean it up.

### 4. A repeatable standard beats a bespoke design, even when every site is a little different

Building to one documented pattern — scaled up or down for HA — made every subsequent deployment faster and reduced the chance of a site-specific mistake.

