# IT Glue documentation structure

Sanitized sample. Fictional client: **Lakeside Professional Group (LPG-DC1)**.

How I organize client documentation so the next technician — or a regional help desk — can pick up the environment without starting from zero.

## Purpose

IT Glue is the system of record. Tickets and RMM tell you what broke. Glue tells you what the environment is supposed to look like, who to call, and how to recover it. If it is not in Glue, it does not exist for anyone but the person who did the work.

## Organization layout

One Organization per client. Locations under the Organization. Everything else related back to those two objects.

| Glue object | What lives here | Example (sanitized) |
|-------------|-----------------|---------------------|
| Organization | Legal name, industry, hours, primary contacts, contract notes | Lakeside Professional Group |
| Locations | Physical / logical sites with address and timezone | LPG-DC1 — primary office / server room |
| Configurations | Servers, firewalls, switches, APs, UPS, workstations of record | LPG-HV01 (Hyper-V host) |
| Flexible Assets | Repeatable stacks: M365, backup, antivirus, ISP, licensing | M365 tenant summary; backup schedule |
| Passwords | Vaulted secrets with owner + rotation notes — never in a document body | Domain Admin (break-glass) |
| Documents | SOPs, runbooks, diagrams, change history | SOP-LPG-004 Hyper-V VM unavailable |
| Related items | Links between configs, docs, assets, and contacts | HV01 related to backup FA + runbook |

## Flexible Asset set for day one

| Flexible Asset | Required fields | Why it matters |
|----------------|-----------------|----------------|
| Internet / WAN | ISP, circuit ID, public IP, gateway, support number | First call on an outage |
| Firewall / VPN | Make/model, firmware, management URL, VPN type | Remote access and perimeter |
| Active Directory / Entra | Domain FQDN, DC names, Entra tenant ID, sync method | Identity is the blast radius |
| Microsoft 365 | Tenant name, licensed SKUs, admin portal, MFA posture | Email / identity incidents |
| Backup | Product, what is protected, schedule, last test restore date | DR is not a checkbox |
| Antivirus / EDR | Product, policy name, exclusions, console URL | Containment and noise control |
| RMM / PSA | Device naming standard, policy names, ticket board | How monitoring becomes work |

## Document types

- **SOP** — repeatable how-to (onboarding a user, imaging a PC, approving a patch window)
- **Runbook** — incident path with decision points (VM down, M365 compromise, VLAN internal-only outage)
- **As-built** — what was actually installed, not the proposal
- **Network / identity diagram** — one page, current, dated
- **Change note** — short record linked to the ticket and the Configuration it touched

## Naming and hygiene

- Documents: `SOP-CLIENT-### Title` — example `SOP-LPG-004 Hyper-V VM Unavailable`
- Configurations: `SITE-ROLE-##` — `LPG-HV01`, `LPG-DC01`, `LPG-FW01`
- Every Configuration has an owner contact, warranty / lifecycle note, and at least one related Document
- Passwords live only in the password vault. Documents say *where* the secret is, never the secret
- Review dates on Flexible Assets (quarterly for backup test restore; after every major change)
- If regional L1 cannot work the ticket from Glue, the local record is incomplete

## Minimum viable client (first 30 days)

1. Organization + Locations + primary contacts
2. Configurations for every server, firewall, and core switch
3. Flexible Assets: WAN, firewall, identity, M365, backup, AV/EDR
4. Two runbooks: after-hours host/VM down, and identity/M365 containment
5. One current network sketch and a device-naming standard

## Related samples in this repo

- [`sop-lpg-004-hyperv-vm-unavailable.md`](./sop-lpg-004-hyperv-vm-unavailable.md) — the Document object
- [`invoke-windows-healthcheck.ps1`](./invoke-windows-healthcheck.ps1) — scheduled detector that feeds operational visibility
