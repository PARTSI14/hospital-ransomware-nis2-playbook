# Hospital Ransomware & Extortion: Incident Response Playbook

**Scope:** an acute-care hospital (emergency department, inpatient wards, labs, imaging) that is an *essential* or *important* entity under NIS2.
**Structure:** NIST SP 800-61 phases, adapted for clinical operations. Every phase has a clinical track and a technical track, because in a hospital **patient safety comes before IT recovery**.

> Times are given as **T0 = the moment the hospital becomes aware of a significant incident**. This is also when the NIS2 reporting clock starts (see [nis2-reporting-timeline.md](nis2-reporting-timeline.md)).

---

## Roles (fill in before an incident)

| Role | Who | Main job |
|---|---|---|
| Incident Commander (IC) | Head of IT / CISO | Runs the technical response, owns the timeline |
| Clinical Lead | Medical Director or ED lead | Decides on ED diversion, downtime procedures, patient transfers |
| Crisis Staff Lead | Managing Director | Final decisions: shutdown, ransom, public statements |
| NIS2 Reporting Officer | Compliance / ISO | Sends early warning, notification, final report on time |
| Data Protection Officer | DPO | GDPR Art. 33/34 assessment and notification |
| Communications | Press office | Staff, patients, media, emergency services |
| Legal | In-house / external counsel | Law enforcement, sanctions checks, insurer, evidence |
| External DFIR | Retainer partner | Forensics, threat-actor contact if needed |

Keep **printed copies** of this table, phone numbers, and the network diagram. During ransomware, e-mail, the intranet and VoIP may be down.

---

## Phase 1: Preparation (before anything happens)

**Clinical**
- Paper downtime kits on every ward and in the ED: admission forms, medication charts, lab request forms, blank wristbands.
- A tested **downtime procedure** for each critical system: EHR/HIS, lab (LIS), imaging (PACS/RIS), pharmacy, patient monitoring.
- Agreement with the regional **emergency dispatch centre** on how ED diversion is requested and lifted.
- A twice-a-year tabletop exercise that simulates **72 hours** of total IT outage, not just one shift.

**Technical**
- Offline or immutable backups of EHR, LIS and PACS databases, plus Active Directory. **Test restores**, and measure how long a full EHR restore really takes.
- Network segmentation: clinical devices (infusion pumps, modalities, monitors) in their own VLANs, with no direct path from office PCs.
- MFA on VPN, remote access tools, and every privileged account.
- Command-line logging (Sysmon or Windows 4688 + command line) shipped to a SIEM that does **not** depend on domain credentials.
- Deploy the pre-encryption tripwires in [`detections/sigma`](../detections/sigma).
- Register with the national NIS2 authority and save the reporting portal login offline.

---

## Phase 2: Detection & Triage (T0 to T0 + 2h)

**Typical first signs in a hospital**
- Clinicians report that the EHR is slow, files won't open, or there are ransom notes on screens.
- SIEM alerts from the tripwires: shadow copy deletion, event log clearing, backup or SQL services stopped, LSASS dump, PsExec fan-out.
- An extortion e-mail or phone call (in the Greiz case, the extortion message arrived the day after the system failure).

**Actions**
1. IC opens an incident record and writes down **T0** (date, time, who noticed). Every later deadline depends on it.
2. Quick scope: how many hosts, which systems (EHR? LIS? PACS?), is encryption still running?
3. Clinical Lead checks for **immediate patient-safety impact**: ED, ICU, OR, lab results, medication administration.
4. Decide whether the incident is **significant** under NIS2 (see the timeline document). In a hospital, any disruption of clinical services almost always counts.
5. Start the NIS2 clock: the NIS2 Reporting Officer prepares the **24h early warning**.

**ATT&CK signals to look for first** (from the layer, used by 4-5 of 5 reference actors):
`T1490` Inhibit System Recovery · `T1486` Data Encrypted for Impact · `T1685` Disable or Modify Tools · `T1070.004` File Deletion · `T1003.001` LSASS Memory · `T1059.001` PowerShell · `T1016` network discovery

---

## Phase 3: Containment (T0 to T0 + 24h)

**Decision point A: isolate segments or pull the internet connection?**

| Option | When | Clinical cost |
|---|---|---|
| Isolate affected VLANs/hosts | Encryption limited to a few segments | Low to medium |
| Cut all external connections | Active C2 / exfiltration, spread unclear | Medium: tele-radiology, lab links and e-prescribing stop |
| Shut down the whole network | Mass encryption in progress (correlation rule fires) | High: all-paper operations, likely ED diversion |

The Clinical Lead must be in the room for this decision. **Do not power off encrypted machines** if you can avoid it. Memory holds keys and evidence; disconnect the network instead.

**Technical**
- Block the identified C2 IPs and domains, and disable compromised accounts.
- Reset the passwords of privileged accounts. Plan a **double krbtgt reset** if domain admin was compromised.
- Disable remote access paths (VPN, RDP gateways, remote support tools) until they are verified clean.
- Preserve evidence: memory images of key hosts, firewall and VPN logs, EDR telemetry. Hash and store them.

**Clinical**
- Activate the downtime procedures. Move to paper.
- **Decision point B: ED diversion.** The Clinical Lead asks the emergency dispatch centre to route ambulances elsewhere. In Greiz, patients were told to call 112 and the dispatch centre in Gera decided which hospital received them.
- Check medical devices: are they still working stand-alone? Are any network-dependent (central monitoring, smart pumps)?
- Prioritise lab and imaging for urgent cases, and run results by courier or phone if needed.

**Reporting (by T0 + 24h)**
- Send the **NIS2 early warning** ([template](../templates/01-early-warning-24h.md)).
- Report to the police / cybercrime unit (in Germany, the State Criminal Police Office, LKA).
- Notify the cyber insurer (many policies require notice within 24-48h).

---

## Phase 4: Eradication (T0 + 24h to days)

- Find **initial access**. The most common for the reference actors: valid accounts on VPN or RDP (`T1078`, `T1133`), exploited edge devices (`T1190`), phishing (`T1566`).
- Find persistence: scheduled tasks, new services, Run keys, new domain accounts, remote access tools (`T1053.005`, `T1547.001`, `T1136`, `T1219`).
- Check for **data theft** before encryption (`T1560.001` archiving, `T1567.002` cloud uploads, unusual outbound volume). Double extortion is the norm, and this drives the GDPR assessment.
- Rebuild, don't clean: reimage compromised hosts from known-good images, and rebuild domain controllers if AD integrity is uncertain.

**Reporting (by T0 + 72h)**
- Send the **NIS2 incident notification** with a severity and impact assessment and IoCs ([template](../templates/02-incident-notification-72h.md)).
- DPO: if personal data was affected, notify the data protection authority under **GDPR Art. 33 within 72 hours**. Inform patients (Art. 34) if there is a high risk to them.

---

## Phase 5: Recovery (days to weeks)

Restore in **clinical priority order**, agreed in advance with the Clinical Lead:

1. Identity (AD, MFA) and core network, in a clean, isolated build
2. EHR/HIS + ED module
3. LIS (lab) and blood bank
4. PACS/RIS (imaging)
5. Pharmacy and medication administration
6. Admin systems (billing, HR, e-mail)

- Restore from backups that were verified clean, and scan restored data before reconnecting.
- Watch for re-infection for at least 30 days with extra monitoring.
- Lift ED diversion only when the Clinical Lead confirms that the ED, lab and imaging can work safely (Greiz reopened its ED gradually about two days after the failure).
- Back-enter paper records into the EHR, and keep the paper originals.

**Decision point C: pay the ransom?** This is the Crisis Staff Lead's decision, with legal counsel. Check:
- sanctions lists (paying a sanctioned group can be illegal),
- law enforcement advice,
- the insurer's position,
- whether working backups make payment unnecessary.

Payment does not guarantee decryption or deletion of stolen data. Document the decision and its reasons either way.

---

## Phase 6: Post-incident (by T0 + ~1 month)

- **NIS2 final report**: root cause, threat type, mitigations applied and ongoing, cross-border impact ([template](../templates/03-final-report-1-month.md)). If the incident is still ongoing at that point, send a **progress report** and the final report within one month after the incident is handled.
- Lessons-learned meeting with IT, clinical staff, management and the DPO.
- Update this playbook, the downtime procedures and the detection rules.
- Map the confirmed attacker techniques onto the ATT&CK layer, and compare them with the reference baseline to find detection gaps.

---

## Quick checklist (print this page)

- [ ] T0 recorded
- [ ] Clinical Lead informed, patient-safety check done
- [ ] Downtime procedures activated
- [ ] ED diversion decided (yes / no) and dispatch centre informed
- [ ] Network containment decision taken and logged
- [ ] Evidence preserved (memory, logs, EDR)
- [ ] Police notified
- [ ] Insurer notified
- [ ] **NIS2 early warning sent (≤ 24h)**
- [ ] **NIS2 incident notification sent (≤ 72h)**
- [ ] **GDPR Art. 33 assessment done (≤ 72h)**
- [ ] Initial access and persistence found
- [ ] Data exfiltration assessed
- [ ] Systems restored in clinical priority order
- [ ] **NIS2 final report sent (≤ 1 month after notification)**
- [ ] Lessons learned held, playbook updated
