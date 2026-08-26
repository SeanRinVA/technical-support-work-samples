# SOP-LPG-004 — Hyper-V VM unavailable

Sanitized runbook. Fictional client: **Lakeside Professional Group (LPG-DC1)**.  
Pattern drawn from production Hyper-V + Windows Server operations. No real host names, IPs, or credentials.

| Field | Value |
|-------|-------|
| Client / site | Lakeside Professional Group / LPG-DC1 |
| Audience | L3 local engineer; regional L1/L2 after Glue is complete |
| Related configs | LPG-HV01 (host), LPG-APP01 / LPG-FILE01 (guests) |
| Related Glue assets | Failover Cluster / Hyper-V FA; Backup FA; AD FA |
| RMM / PSA | Alert + ticket. Do not close until Glue change note is updated |
| Severity | P2 if one guest; P1 if host, cluster, or shared storage |
| Last reviewed | August 2026 (sample) |

## 1. Purpose

Restore a Hyper-V guest — or confirm the host/cluster is the real fault — without guessing, without undocumented changes, and without leaving the next technician blind.

## 2. Safety

- Do not delete checkpoints or storage paths until you know why the VM is down
- Do not Live Migrate or Quick Migrate during an active storage fault
- Do not reboot the host during business hours without a client ack unless the host is already hard-down
- Record every action in the ticket as you go. Update Glue when the incident is stable

## 3. Identify

1. Read the ticket / RMM alert. Note host, VM name, start time, and whether other VMs on the same host are healthy
2. Open Glue: LPG-HV01 Configuration, Hyper-V Flexible Asset, and this runbook
3. Confirm the reported name matches Glue (hostname vs display name vs DNS)
4. If multiple VMs or the host itself is down, escalate severity to P1 and treat this as a host/storage event

## 4. Isolate — guest vs host vs identity vs network

### 4.1 Can you reach the host?

- Ping / RDP / WinRM to LPG-HV01 using the management address in Glue
- If the host is reachable and Hyper-V Manager / `Get-VM` shows the guest Off, Saved, or Failed — stay on the guest path (section 5)
- If the host is unreachable but other site systems answer — suspect host OS, cluster node, or host NIC/iSCSI. Go to section 6
- If nothing at the site answers except public internet from a laptop — this is not a single VM. Stop and treat as site network / firewall / ISP

### 4.2 DNS vs IP (common false lead)

- If the guest answers by IP but not by name: check A record, scavenging, host file on the tester, and whether the guest still has the address Glue documents
- Do not rebuild the VM to fix a DNS record

## 5. Guest path

1. On the host: `Get-VM -Name <guest>`. Note State, Status, CPU, memory, and checkpoint presence
2. If Saved or Paused: determine whether this was an intentional host action (patch window) before resuming
3. If Off: check Hyper-V-VMMS and Hyper-V-Worker event logs around the stop time. Look for storage, VHD lock, or integration-service errors
4. Confirm the VHDX / shared-storage path is online and has free space. A full CSV or volume presents as a mysterious guest failure
5. If the guest OS is running but the role is dead (file share, IIS, SQL application stack): treat as a Windows service / disk / identity problem inside the guest, not a hypervisor problem
6. Start the VM only after the storage path is healthy. Watch first boot in Hyper-V Manager / `Get-VM`
7. Validate from a user-facing test: name resolution, share or URL, and one business transaction if this is an application server

## 6. Host / cluster path

- `Get-ClusterNode` and `Get-ClusterGroup` if this host is a failover cluster member
- If one node is Down and groups moved: confirm the surviving node is healthy before you chase the original guest
- If storage (CSV / iSCSI target) is Offline: do not start VMs. Fix presentation first
- Host Event Viewer: System + Hyper-V-VMMS. Look for disk, teaming, and cluster heartbeats — not just the VM name
- If the host requires a restart: drain roles if clustered; notify the client; use the change window in Glue

## 7. Communication

- **P2:** update the ticket within 15 minutes with what you know and the next check
- **P1:** voice or SMS to the client technical contact listed in Glue, then ticket
- Never promise an ETA you have not validated against storage and cluster state

## 8. Close and document

1. Root cause in one sentence (guest OS, storage, host, DNS, or change)
2. What was done. What was not done
3. Glue updates: Configuration notes, any changed IPs or storage paths, last-incident date on the Hyper-V Flexible Asset
4. If this will recur (disk growth, checkpoint buildup), open a follow-up ticket — do not hide it in the resolved note

## Related sample

Scheduled detection uses [`invoke-windows-healthcheck.ps1`](./invoke-windows-healthcheck.ps1) (disk, services, VM state, optional cluster). The script is a detector and logger. This runbook is the human path after the detector fires.
