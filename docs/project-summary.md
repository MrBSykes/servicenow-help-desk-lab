# Project Summary: ServiceNow Help Desk Lab

A hands-on IT service management lab built on a ServiceNow Personal Developer Instance (PDI), Australia release. It follows the same workflow as my earlier osTicket help desk lab, using the ITSM tooling common in enterprise and federal service desks.

The step-by-step build, with screenshots, is in the [README](../README.md). This document summarizes what was built, what the evidence shows, and what was left out.

## What I built

| Phase | What exists in the instance | Evidence |
|---|---|---|
| 1. Access | 3 users (admin, Tier 1 agent, end user), the Home Lab Help Desk group, and the `itil` role granted at the group level | Screenshots 01-10 |
| 2. Incident setup | 4 custom Category choices; the Impact x Urgency priority matrix checked against the instance's lookup rules | 11-18 |
| 3. SLAs | A custom 24x7 schedule; 8 SLA definitions (Response and Resolution for priorities 1-4); one end-to-end P1 test | 19-26 |
| 4. Knowledge | One knowledge base with 4 categories and 5 published articles from real home lab problems | 27-30 |
| 5. CMDB | 5 CIs in 4 classes, 4 relationships, a dependency map, and a P1 incident linked to a CI and a knowledge article | 31-36 |

## Design decisions

- **Roles at the group level.** `itil` is attached to the Home Lab Help Desk group with inheritance on, so any agent added later gets incident access without individual role assignments.
- **Custom categories added beside the defaults.** Home Server, Network/DNS, Penetration Testing and Account Access cover the services in my lab, without removing anything built in.
- **SLA targets chosen for realism.** P1 and P2 run on a 24x7 schedule, and P3 and P4 on business hours. The targets are lab-defined; see [`sla-plan.md`](sla-plan.md).
- **Real incidents as knowledge articles.** Each article records a problem I actually solved: a Pi-hole DNS outage, Pi-hole blocking a link, a UEFI boot USB, DDR5 blue screens, and ransomware triage.
- **A small CMDB with a real dependency chain.** Five CIs model one real outage: Pi-hole runs on the home server, which connects to the router, and two services depend on Pi-hole.

## Results

- **Priority matrix verified.** Impact 3 with Urgency 3 resolves to Priority 4 on this release, not 5, so the lab covers priorities 1-4.
- **P1 SLA tested end to end.** On incident INC0010006, the Response SLA completed when the incident was assigned. The Resolution SLA paused when the state was set to On Hold and completed on resolve, with 1 minute elapsed against the 4-hour target.
- **A defect was found and fixed through testing.** The first test run showed the Resolution SLA stopping at assignment, because its stop condition was wrong. Checking the Task SLA stop time, and not only the incident's activity log, exposed it. All four Resolution definitions now stop on State is Resolved.
- **Impact analysis demonstrated.** The dependency map from Pi-hole shows the server, the router, and the two services above it. A P1 incident (INC0010007) was filed against the Pi-hole CI, linked to KB0010001, and resolved with notes citing the article.

## Problems and fixes

| Problem | Fix |
|---|---|
| The original instance was reclaimed after inactivity | Requested a new instance and rebuilt Phase 1 from my own documentation |
| Group Manager rejected as an invalid reference | Created the users first, then set the Manager |
| No 24x7 schedule on this release | Built one with an all-day daily entry |
| SLA Resolution stopped at assignment | Corrected the stop condition and re-tested on a new incident |
| CIs created in the wrong class | Deleted them and recreated each from its class list |

The full table is in the README's troubleshooting log.

## Limitations

This lab is deliberately honest about its scope:

- Only P1 was tested end to end. P2-P4 definitions exist but were not exercised.
- Escalation notifications at 50, 75 and 100 percent, and a deliberate SLA breach test, were planned but not built.
- The CMDB is a core set of 5 of the 17 planned CIs. Gaming PC, Laptop, SYKES-KALI, Jellyfin, Plex, Pelican Panel, the three VMs and three of the services are not entered.
- The instance's built-in demo SLA for P1 also attaches to P1 incidents. It is noted in the results and not part of my design.
- This is a single-instance simulation with fictional users, not a production environment.

## Rebuild checklist

PDIs are reclaimed after inactivity, so this is the order to rebuild everything on a fresh instance.

1. **Users:** `bsykes.admin` (Bryan Sykes), `sam.okafor` (Sam Okafor), `kate.olsen` (Kate Olsen). Use `.local` placeholder emails.
2. **Group:** Home Lab Help Desk. Manager Bryan Sykes, member Sam Okafor, role `itil` with Inherits on.
3. **Incident categories** (Choices table, Table `incident`, Element `category`):

   | Label | Value | Sequence |
   |---|---|---|
   | Home Server | `home_server` | 200 |
   | Network/DNS | `network_dns` | 210 |
   | Penetration Testing | `pen_testing` | 220 |
   | Account Access | `account_access` | 230 |

4. **Schedule:** `24x7`, one all-day daily entry.
5. **SLA definitions** (incident table): Response and Resolution for each priority, with the durations from [`sla-plan.md`](sla-plan.md). Start is Priority is X, pause is State is On Hold. Response stops when Assigned to is not empty, and Resolution stops when State is Resolved.
6. **Knowledge base:** Home Lab IT Knowledge Base, owner Bryan Sykes, managers Bryan Sykes and Sam Okafor, Instant Publish. Categories: Network & DNS, Hardware, Operating Systems, Security. Then the five articles from [`kb-articles.md`](kb-articles.md).
7. **CIs:** SYKESHOMESERVER (Computer), Home Router (Network Gear), Pi-hole (Application), Home DNS and Ad Blocking and Home Lab Network Services (both Service). Create each from its own class list and check the form header before filling it in.
8. **Relationships:** Pi-hole Runs on SYKESHOMESERVER; SYKESHOMESERVER Connected by Home Router; Home DNS and Ad Blocking Depends on Pi-hole; Home Lab Network Services Depends on Home DNS and Ad Blocking.
9. **Backup:** export an update set after each phase and commit it to the repository.

## Skills demonstrated

ServiceNow administration, role-based access control, incident categorization and priority, SLA design and testing, knowledge management, CMDB design with relationships and dependency mapping, structured troubleshooting, and clear technical documentation.
