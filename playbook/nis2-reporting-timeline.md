# NIS2 Incident Reporting for Hospitals: The 24h / 72h / 1-month Clock

**Legal basis:** Article 23 of Directive (EU) 2022/2555 (NIS2). Healthcare providers fall under the *Health* sector in **Annex I** (sectors of high criticality).

> This is a practical summary for incident responders, not legal advice. National transpositions add their own forms, portals and details. Always check your national law and authority guidance.

---

## 1. Is it a "significant incident"?

Under Art. 23(3), an incident is significant if it:
- has caused, or can cause, **severe operational disruption** of the services or financial loss for the entity, **or**
- has affected, or can affect, other people by causing **considerable material or non-material damage**.

For a hospital, ransomware that takes down the EHR, the lab or the ED meets this test almost every time. Patients who cannot be treated, or are diverted, is "considerable damage". **When in doubt, report.** The early warning is short, and late reporting is the bigger risk.

---

## 2. The clock

**T0 = the moment the entity becomes aware of the significant incident.**

| Deadline | Report | What it must contain (Art. 23(4)) |
|---|---|---|
| **≤ 24 hours** after T0 | **Early warning** | Whether the incident is suspected to be caused by unlawful or malicious acts, and whether it could have a cross-border impact |
| **≤ 72 hours** after T0 | **Incident notification** | Update of the early warning, an initial assessment of severity and impact, and indicators of compromise where available |
| On request | **Intermediate report** | Status updates requested by the CSIRT or authority |
| **≤ 1 month** after the incident notification | **Final report** | Detailed description incl. severity and impact; type of threat or root cause; mitigation measures applied and ongoing; cross-border impact |
| If still ongoing at 1 month | **Progress report**, then final report **within 1 month after the incident is handled** | Same content as the final report, as far as known |

The CSIRT or authority must reply to the early warning without undue delay and, **where possible, within 24 hours**, with initial feedback and, on request, guidance on mitigation.

### Parallel obligations (often forgotten)

| Obligation | Deadline | Recipient |
|---|---|---|
| GDPR Art. 33 personal data breach notification | ≤ 72h after becoming aware | Data protection authority |
| GDPR Art. 34 communication to data subjects | Without undue delay, if high risk | Affected patients |
| NIS2 Art. 23(1) notice to service recipients | Without undue delay, where appropriate | Patients / referrers who may be affected |
| Police report | As soon as possible | Cybercrime unit |
| Cyber insurance notice | Per policy, often 24-48h | Insurer |

**One incident, at least two clocks.** The NIS2 72h notification and the GDPR 72h notification go to **different authorities**, on different forms. Prepare both in parallel.

---

## 3. National examples

### Germany: NIS2UmsuCG / BSIG
- The NIS2 Implementation and Cybersecurity Strengthening Act (NIS2UmsuCG) rewrote the BSI Act (BSIG) and **entered into force on 6 December 2025**.
- Reports go to the **BSI** (Federal Office for Information Security). § 32 BSIG sets a **three-stage reporting duty** for significant incidents, which mirrors the 24h / 72h / 1-month model.
- Hospitals are in scope; larger ones are also operators of critical facilities (KRITIS), with extra duties.

### Greece: Law 5160/2024
- NIS2 was transposed by **Law 5160/2024** (Government Gazette A' 195/27-11-2024).
- Reports go to the **National Cybersecurity Authority** (Εθνική Αρχή Κυβερνοασφάλειας). Article 16 repeats the 24h early warning, 72h notification and final report structure of the Directive.
- The Authority publishes a reporting guide for entities (cyber.gov.gr).

---

## 4. Illustration: the clock applied to a hospital incident like Greiz

Public timeline of the Greiz-Ronneburg incident (Thuringia, Germany):
- **Sat 14 Nov 2026, evening**: system failure noticed
- **Sun 15 Nov, morning**: ED out of service; extortion message received that day
- **Mon 16 Nov, evening**: ED starts to reopen gradually

If an entity in this situation set **T0 = Sun 15 Nov 2026, 09:00** (when the intrusion was confirmed), the NIS2 deadlines would be:

| Report | Deadline |
|---|---|
| Early warning | **Mon 16 Nov, 09:00** |
| Incident notification | **Wed 18 Nov, 09:00** |
| Final report | **One month after the notification is submitted** (≈ 18 Dec 2026 if it was sent on the 72h deadline) |

> **Illustration only.** We do not know the hospital's NIS2 classification or what it actually reported, and we make no claim about its compliance. T0 could also be argued as Saturday evening, when the failure was first noticed. That would move every deadline about 12 hours earlier. This is exactly why the playbook says: **write down T0 the moment you suspect an incident**.

See the visual version in [`../hospital-ransomware-nis2-clock.png`](../hospital-ransomware-nis2-clock.png).
