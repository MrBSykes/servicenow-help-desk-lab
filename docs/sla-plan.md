# Incident Priority Matrix and SLA Plan

## 1. Priority matrix (Impact × Urgency)

ServiceNow calculates Priority from Impact and Urgency. This table was verified against the instance's Priority Data Lookup rules (see screenshot 17). Impact 3 + Urgency 3 resolves to **4 - Low** on this release, so incidents never reach Priority 5.

| | Urgency 1 (High) | Urgency 2 (Medium) | Urgency 3 (Low) |
|---|---|---|---|
| **Impact 1 (High)** | 1 - Critical | 2 - High | 3 - Moderate |
| **Impact 2 (Medium)** | 2 - High | 3 - Moderate | 4 - Low |
| **Impact 3 (Low)** | 3 - Moderate | 4 - Low | 4 - Low |

**Lab definitions**
- **Impact 1 (High):** a whole-network or multi-user service is down (DNS, router, domain controller).
- **Impact 2 (Medium):** one service or one department/group of users affected.
- **Impact 3 (Low):** a single user or a single non-essential device.
- **Urgency 1 (High):** work cannot continue, no workaround.
- **Urgency 2 (Medium):** work degraded, workaround exists.
- **Urgency 3 (Low):** minor inconvenience.

## 2. SLA targets

| Priority | Name | Response target | Resolution target | Schedule |
|---|---|---|---|---|
| 1 | Critical | 1 hour | 4 hours | 24x7 |
| 2 | High | 2 hours | 8 hours | 24x7 |
| 3 | Moderate | 8 hours (1 business day) | 3 business days | 8-5 weekdays |
| 4 | Low | 1 business day | 5 business days | 8-5 weekdays |

These are lab-defined targets, chosen to be realistic for a help desk. Priority 5 is not used because the lookup rules on this release never produce it.

## 3. SLA definitions to build (Service Level Management > SLA Definitions > New)

Create one **Response** and one **Resolution** definition per priority 1-4 (8 total), or start with just P1 and P2 if you're short on time.

| Field | Value |
|---|---|
| Name | e.g. `P1 Resolution - 4 Hours` |
| Type | SLA |
| Target | Response or Resolution |
| Table | Incident |
| Duration type | User specified |
| Duration | per table above |
| Schedule | `24x7` (created manually, since this release has no built-in 24x7 schedule) or an `8-5 weekdays` schedule |
| Start condition | Priority is 1 (set per definition) |
| Pause condition | State is On Hold |
| Stop condition | Response: Assigned to is not empty (or State is In Progress). Resolution: State is Resolved or Closed |
| Retroactive start | Unchecked |

## 4. Escalation and notification checkpoints

| Point | Action |
|---|---|
| 50% elapsed | Visible warning on the task SLA record |
| 75% elapsed | Email notification to Home Lab Help Desk group |
| 100% (breach) | Email to group manager (`bsykes.admin`) |

## 5. Test plan (this becomes your screenshot set)

1. Create a P1 incident as Kate Olsen (impact 1, urgency 1) and confirm Priority = 1 and the P1 SLAs attach.
2. Assign it to Home Lab Help Desk / Sam Okafor and confirm the Response SLA stops.
3. Set the incident to On Hold and show the SLA clock pausing.
4. Resolve the incident inside the target and show the SLA marked **Achieved**.
5. Create a second incident and let a short test SLA (set to 2-5 minutes) breach to show **Breached** status and the notification.

**Screenshots to capture:** priority matrix result on a new incident, the SLA definition form, the Task SLA related list on an incident (active), one Achieved, one Breached.
