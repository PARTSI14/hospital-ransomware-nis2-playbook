# NIS2 Incident Notification (≤ 72 hours after awareness)

> Template. Updates the early warning (Art. 23(4)(b)). Reference the early warning ID.

| Field | Value |
|---|---|
| Entity / registration ID | `[...]` |
| Early warning reference | `[ID / date-time sent]` |
| Time of awareness (T0) | `[...]` |
| Time of this report | `[...]` |

**1. Updated description**
`[What happened, when it started (if known), how it was detected.]`

**2. Initial severity assessment**
- Severity: `[Low / Medium / High / Critical]` and why
- Systems affected: `[EHR, LIS, PACS, AD, e-mail, ...]`
- Number of hosts affected: `[approx.]`
- Encryption: `[yes / no / partial]` Data exfiltration: `[confirmed / suspected / no evidence]`

**3. Impact assessment**
- Patient care: `[ED diversion hours, postponed procedures, transfers]`
- Duration so far and expected recovery time: `[...]`
- Personal data affected: `[yes / no / under investigation]`. GDPR Art. 33 notification to the DPA: `[sent on ... / not required because ...]`
- Third parties / suppliers affected: `[...]`

**4. Indicators of compromise** (where available)
| Type | Value | Notes |
|---|---|---|
| IP | `[x.x.x.x]` | C2 / exfiltration |
| Domain | `[...]` | |
| File hash (SHA-256) | `[...]` | Encryptor / tool |
| Ransom note name | `[...]` | |
| Accounts abused | `[...]` | Do not share passwords |

**5. ATT&CK techniques observed so far**
`[e.g. T1133 External Remote Services, T1003.001 LSASS Memory, T1569.002 Service Execution, T1490 Inhibit System Recovery, T1486 Data Encrypted for Impact]`

**6. Measures taken and planned**
`[Containment, eradication steps, recovery plan and priorities.]`

**7. Threat actor**
`[Claimed group / unknown. Note: attribution is preliminary.]`
