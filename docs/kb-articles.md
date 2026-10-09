# Home Lab Knowledge Base: Articles

Five articles drawn from real home lab troubleshooting. Each section maps to a field on the ServiceNow Knowledge Article form.

**Status:** all five are published in the instance (see the Phase 4 section of the README). Headings use the article numbers ServiceNow assigned; there is no KB0010003 (that number was consumed without a published article).

**Setup (do once):** Knowledge > Knowledge Bases > New. Name it `Home Lab IT Knowledge Base`, owner `bsykes.admin`, managers `Home Lab Help Desk`. Create categories: `Network & DNS`, `Hardware`, `Operating Systems`, `Security`. Publish each article after review (Draft > Review > Published).

---

## KB0010001: Network-wide internet outage after a static IP change (Pi-hole DNS)

- **Category:** Network & DNS
- **Short description:** Every device loses internet at once after a server's static IP was changed.
- **Audience:** Tier 1 Support

**Symptoms**
- All devices on the network can't load websites, but the router is reachable.
- Devices show "connected, no internet" or DNS errors.
- Started right after a network setting was changed on the server that runs Pi-hole.

**Cause**
The router hands out the Pi-hole server as the network's DNS. A typo in the server's static IP (wrong address, subnet, or gateway) meant Pi-hole was no longer reachable at the address clients expected, so every DNS lookup failed.

**Resolution**
1. From any client, run `ping 8.8.8.8`. If it succeeds but `nslookup google.com` fails, the problem is DNS, not the connection.
2. Temporarily set one client's DNS to `1.1.1.1` to confirm internet access works.
3. Log in to the Pi-hole server (console or Remote Desktop) and check its network adapter settings.
4. Correct the static IP, subnet mask, and gateway to match the intended values and the router's DHCP reservation.
5. Restart the Pi-hole service or container and confirm it responds.
6. Reset client DNS to automatic.

**Verification**
`nslookup google.com` resolves from multiple devices, and the Pi-hole dashboard shows queries coming in.

**Prevention**
Document the intended static IP in the CMDB record for the server. Set a router DHCP reservation instead of relying only on a manual static IP.

**Related CIs:** Pi-hole, SYKESHOMESERVER, Home Router

---

## KB0010002: A website or deal link is blocked or breaks when using Pi-hole

- **Category:** Network & DNS
- **Short description:** A legitimate link or page fails to load or won't redirect correctly on the home network.
- **Audience:** Tier 1 Support

**Symptoms**
- A specific page, coupon/affiliate link, or redirect fails while the rest of the web works.
- The link works on mobile data or off the home network.

**Cause**
Pi-hole blocklists sometimes include tracking or redirect domains that some legitimate links depend on (affiliate and cashback redirects are a common example).

**Resolution**
1. Reproduce the problem, then open the Pi-hole admin page > **Query Log**.
2. Find the entries marked **Blocked** at the time of the failure and note the domain.
3. Confirm the domain is one the link actually needs (for example an affiliate network such as Partnerize or Rakuten, or Honey).
4. Add the domain to the **Allowlist** (Domains > Add to allowlist).
5. Retry the link.

**Verification**
The link loads and the Query Log now shows the domain as **Allowed**.

**Notes**
Allowlist only domains you've confirmed are needed. Each exception slightly reduces blocking coverage.

**Related CIs:** Pi-hole

---

## KB0010004: Bootable USB won't create or won't boot in UEFI mode

- **Category:** Operating Systems
- **Short description:** The installer USB fails to write, is write-protected, or the PC won't boot from it.
- **Audience:** Tier 1 Support

**Symptoms**
- The USB tool reports a write failure.
- Windows says the disk is write-protected.
- The PC ignores the USB or won't boot from it in UEFI mode.

**Cause**
A write tool that doesn't handle the target system's UEFI/GPT requirements, or leftover partition or protection state on the drive.

**Resolution**
1. If the first tool fails, retry with **Rufus**. Set Partition scheme to **GPT** and Target system to **UEFI (non-CSM)**.
2. If the drive reports write-protection, open an elevated Command Prompt and run `diskpart`:
   - `list disk`, then `select disk X` (confirm the number carefully)
   - `attributes disk clear readonly`
   - `clean`
   - `convert mbr`, then recreate and format the partition
3. Write the ISO again.
4. Boot menu (F2/F12/Esc depending on the board): choose the USB entry labeled **UEFI**.

**Verification**
The installer menu loads from the USB.

**Warning:** `clean` erases the selected disk. Double-check the disk number first.

**Related CIs:** SYKES-KALI

---

## KB0010005: Random blue screens (MEMORY_MANAGEMENT) after a BIOS update

- **Category:** Hardware
- **Short description:** Repeated MEMORY_MANAGEMENT BSODs on a DDR5 AMD system after a BIOS update.
- **Audience:** Tier 1 Support, Tier 2 escalation

**Symptoms**
- Repeated `MEMORY_MANAGEMENT` blue screens, often under load or gaming.
- Began after a BIOS update.
- RAM passes basic checks.

**Cause**
The BIOS update changed memory/fabric clock settings (FCLK) to values that were unstable on this system.

**Resolution**
1. Enter BIOS and load the memory profile (EXPO/XMP) if the update reset it.
2. Set **FCLK** manually to **2000 MHz** (the stable value on this system) instead of Auto.
3. Save, reboot, and stress test (for example MemTest86 or a long gaming session).
4. If it persists, test with a single RAM stick and check for a newer BIOS.

**Verification**
No BSODs across several days of normal use and a clean memory stress test.

**Notes**
Always note the BIOS version and settings before and after changes.

**Related CIs:** Gaming PC

---

## KB0010006: Triage for a suspected ransomware infection and drive health check

- **Category:** Security
- **Short description:** Steps to contain a suspected ransomware infection and confirm whether a drive is still healthy.
- **Audience:** Tier 1 Support, escalate to Security

**Symptoms**
- Files renamed with unfamiliar extensions, a ransom note, or files that won't open.

**Resolution**
1. **Contain:** disconnect the device from the network (unplug Ethernet, disable Wi-Fi). Do not power off unless instructed.
2. **Don't pay or contact the attacker.** Photograph the ransom note for evidence.
3. Identify scope: which drives and shares are affected. Check connected backups and disconnect them if exposed.
4. Scan from clean media with an offline or bootable antimalware scanner.
5. If the OS is compromised, wipe and reinstall rather than cleaning in place. Restore data only from backups that predate the infection.
6. Check the physical drive with **CrystalDiskInfo** (health status, reallocated and pending sectors). A healthy drive can be reformatted and reused.
7. Reset credentials used on the device from a clean machine.

**Verification**
A clean scan, a freshly installed OS, and drive health reported as Good.

**Escalation:** Create a Security Incident (or Problem record) for root cause review.

**Related CIs:** the affected device, backup drive
