# CMDB Plan: Home Lab

Goal: a small but realistic CMDB that mirrors the real home lab, with relationships that make impact analysis work. Items marked **(confirm)** are things I'm unsure about, so check them against your actual setup before entering them.

## 1. Business service (top of the tree)

| CI | Class | Notes |
|---|---|---|
| Home Lab Network Services | Business Service | Parent service everything rolls up to |
| Home DNS and Ad Blocking | Business Service | Depends on Pi-hole |
| Home Media Services | Business Service | Jellyfin, Plex |
| Home Game Hosting | Business Service | Pelican Panel with Minecraft/Palworld |
| Home Lab Identity & Security Lab | Business Service | Active Directory, Security Onion, osTicket |

## 2. Hardware CIs

| Name | Class | Key attributes | Notes |
|---|---|---|---|
| SYKESHOMESERVER | Computer | Windows 11 Pro, Ryzen CPU, 32 GB RAM | Hosts containers and VMs. **(confirm)** CPU model and storage |
| SYKES-KALI | Computer | ASUS X550Z, AMD A8-7100, 8 GB RAM, 256 GB SSD | Now running Ubuntu Desktop. **(confirm)** whether you want to keep the hostname |
| Gaming PC | Computer | Ryzen 5 9600X, RTX 4080 Super, DDR5 | **(confirm)** hostname |
| Laptop | Laptop | | **(confirm)** make/model |
| Home Router | Network Gear | Verizon G3100 | **(confirm)** model |

## 3. Software and service CIs

| Name | Class | Runs on |
|---|---|---|
| Pi-hole | Application | SYKESHOMESERVER (Docker) |
| Jellyfin | Application | SYKESHOMESERVER (Docker) |
| Plex | Application | SYKESHOMESERVER (Docker) |
| Pelican Panel | Application | SYKESHOMESERVER (Docker/WSL2) |
| Active Directory VM | Virtual Machine | SYKESHOMESERVER |
| Security Onion VM | Virtual Machine | SYKESHOMESERVER |
| osTicket VM | Virtual Machine | SYKESHOMESERVER |

Table names can vary by instance. If a specific class isn't available, use the closest generic one (Application, Computer) rather than creating custom classes.

## 4. Relationships

Create these in each CI's **Related Items** (or via the CI Relationship Editor).

| Parent CI | Relationship | Child CI |
|---|---|---|
| Pi-hole | Runs on::Runs | SYKESHOMESERVER |
| Jellyfin | Runs on::Runs | SYKESHOMESERVER |
| Plex | Runs on::Runs | SYKESHOMESERVER |
| Pelican Panel | Runs on::Runs | SYKESHOMESERVER |
| Active Directory VM | Hosted on::Hosts | SYKESHOMESERVER |
| Security Onion VM | Hosted on::Hosts | SYKESHOMESERVER |
| osTicket VM | Hosted on::Hosts | SYKESHOMESERVER |
| SYKESHOMESERVER | Connected by::Connects to | Home Router |
| Gaming PC | Connected by::Connects to | Home Router |
| SYKES-KALI | Connected by::Connects to | Home Router |
| Laptop | Connected by::Connects to | Home Router |
| Home DNS and Ad Blocking | Depends on::Used by | Pi-hole |
| Home Media Services | Depends on::Used by | Jellyfin, Plex |
| Home Game Hosting | Depends on::Used by | Pelican Panel |
| Home Lab Network Services | Depends on::Used by | Home DNS and Ad Blocking, Home Media Services, Home Game Hosting |

## 5. Impact analysis demo (the portfolio payoff)

1. Open a P1 incident titled something like "Network-wide DNS failure" and set its **Configuration item** to `Pi-hole`.
2. Open the Pi-hole CI and view the **dependency map** (or **Show related items**) to show it affects Home DNS and Ad Blocking and the Home Lab Network Services above it.
3. Link the incident to **KB0001** using the Knowledge attach button, and resolve it with notes.

This mirrors a real outage you resolved, so the walkthrough tells a true story.

## 6. Data quality notes

- Fill Name, Class, Operational status, and Assigned to (`bsykes.admin`) on every CI.
- Add Serial number and IP address only where you're sure of them.
- Avoid publishing real IP addresses or serial numbers in screenshots or the GitHub README. Redact or use lab-only values.

## 7. Screenshots to capture

CI list showing all CIs, one CI form (SYKESHOMESERVER), the relationships list, the dependency map, and the incident with its CI and attached KB article.
