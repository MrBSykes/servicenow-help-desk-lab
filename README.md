# ServiceNow Help Desk Lab

Hands-on IT service management lab built on a ServiceNow Personal Developer Instance (PDI), modeled on my earlier [osTicket Help Desk Lab](https://github.com/MrBSykes). The goal is to practice the workflows a Tier 1 / Tier 2 service desk runs every day: role-based access, incident handling, SLAs, a knowledge base, and a CMDB.

**Release:** Australia (PDI) | **Author:** Bryan Sykes | **Status:** Phase 1 complete, Phases 2-5 in progress

---

## Lab scope and status

| Phase | Scope | Status |
|---|---|---|
| 1 | Users, groups, and role-based access (`itil`) | Complete |
| 2 | Incident categories and priority matrix (impact x urgency) | In progress |
| 3 | SLA definitions, escalation, and breach testing | Planned. See [`docs/sla-plan.md`](docs/sla-plan.md) |
| 4 | Knowledge base with 5 articles from real troubleshooting | Drafted. See [`docs/kb-articles.md`](docs/kb-articles.md) |
| 5 | CMDB of the home lab with relationships and impact analysis | Planned. See [`docs/cmdb-plan.md`](docs/cmdb-plan.md) |

Phases 3-5 are designed but not yet built. I'll update this table and add screenshots as each one is completed.

---

## Environment

- ServiceNow Personal Developer Instance, Australia release, free tier
- Simulated team on one instance, using fictional personas so the ticket workflow reads like a real multi-person desk

| Persona | User ID | Role in the lab |
|---|---|---|
| Bryan Sykes | `bsykes.admin` | System administrator and group manager |
| Sam Okafor | `sam.okafor` | Tier 1 Support Agent (inherits `itil` through group membership) |
| Kate Olsen | `kate.olsen` | End user who submits tickets |

---

## Phase 1: Instance setup, users, and groups

### 1. Request and provision the instance

Requested a Personal Developer Instance from the ServiceNow Developer portal and waited for provisioning.

![Instance setup in progress](screenshots/01-pdi-setup-in-progress.png)

Instance online, with the Australia release, admin account, and 45 available plugins.

![PDI online](screenshots/02-pdi-provisioned-online.png)

### 2. Create the Home Lab Help Desk group

Created the assignment group with a name and description. Entering `bsykes.admin` in the Manager field before that user existed failed with **Invalid reference**, because Manager is a reference to a User record and not a free-text field.

![Invalid reference on Manager field](screenshots/03-group-invalid-reference.png)

**Fix:** cleared Manager, saved the group, and created the users first. Manager was set afterward (step 5).

<!-- TODO: retake #04 after clicking Submit: the saved Group record with the "Record saved" banner -->
<!-- ![Group saved](screenshots/04-group-saved.png) -->

### 3. Find the Users table

The left navigator didn't surface **User Administration** on my instance. Rather than keep searching the menu, I opened the table directly by URL (`/sys_user_list.do`), which loads the Users list regardless of menu layout.

![Users table opened by direct URL](screenshots/05-users-table-direct-url.png)

### 4. Create the three personas

Each user was created with User ID, first and last name, a `.local` placeholder email, and a title. Department and Manager were left blank.

<!-- TODO: retake #06, #07, #08 after Submit: the saved user records (or the Users list filtered to each user) -->
<!-- ![bsykes.admin](screenshots/06-user-bsykes-admin.png) -->
<!-- ![sam.okafor](screenshots/07-user-sam-okafor.png) -->
<!-- ![kate.olsen](screenshots/08-user-kate-olsen.png) -->

### 5. Set the manager and add the agent

With `bsykes.admin` now existing, Manager on the group resolved to **Bryan Sykes**, and **Sam Okafor** was added under Group Members.

![Group manager and member](screenshots/09-group-manager-and-member.png)

### 6. Grant the `itil` role at the group level

Attached the `itil` role to the group (Inherits = true) so every current and future member gets incident-handling access without per-user role assignments.

![itil role on the group](screenshots/10-group-itil-role-added.png)

---

## Troubleshooting log

| Issue | Cause | Resolution |
|---|---|---|
| "Invalid reference" on the group Manager field | Manager is a User reference, and the user did not exist yet | Created users first, then set Manager |
| User Administration missing from the navigator | Menu layout and filtering differed on this instance | Opened the table directly with `/sys_user_list.do` |
| Instance not found after a long break | PDIs are reclaimed after a period of inactivity (confirmed by the notification email) | Requested a new instance and rebuilt Phase 1 from this documentation |

## Lessons learned

- **Dependency order matters.** Reference fields need their target records to exist first, so users come before group managers and memberships.
- **PDIs are reclaimed when idle.** Treat the instance as disposable: export configuration as an **update set** and commit it to this repo so a rebuild takes minutes, not hours. Log in regularly while the project is active.
- **Assign roles to groups, not individuals.** Group-level role inheritance is easier to audit and scales as the team grows.

---

## Repository layout

```
.
├── README.md
├── docs/
│   ├── kb-articles.md
│   ├── sla-plan.md
│   └── cmdb-plan.md
└── screenshots/
```

## Skills demonstrated

ServiceNow administration (users, groups, roles, reference fields), role-based access control, ITIL-aligned incident categorization and priority, SLA design, knowledge management, CMDB design, structured troubleshooting and documentation.

