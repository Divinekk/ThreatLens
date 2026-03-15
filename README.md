# ThreatLens — Elastic Security Monitoring Exercise

Hands-on SIEM practice using Elastic Security on Elastic Cloud. Real log analysis, custom detection rules, and a formal incident triage report

> "You cannot defend what you cannot detect. This is the detection layer."

---

## About This Project

Real-world security operations workflow using the ELK Stack (Elasticsearch, Kibana, Elastic Security). Ingested web server logs, identified attack patterns, built a custom brute force detection rule, and produced a formal incident triage report — the same workflow a SOC analyst runs daily.

This exercise is the third layer of a connected body of work:

- **Thesis** — identified the cryptographic vulnerabilities
- **Vault-API** — implemented the defenses in code
- **ThreatLens** — built the detection layer to catch attacks in progress

**[Read the Incident Triage Report](./Incident_Triage_Report_BruteForce_Divine.pdf)**

---

## What I Built

**Log Analysis**
Ingested Apache-style web server logs into Elasticsearch. Queried 1,600+ entries using Kibana Discover. Identified response code distributions, filtered Nigerian-origin traffic, and isolated high-frequency error patterns by source IP.

**Attack Pattern Detection**
Identified repeated 404 responses indicating directory enumeration and endpoint probing. Aggregated requests by source IP to surface the highest-volume suspicious actors. Explored Elastic Security Overview dashboard and enabled prebuilt web attack detection rules.

**Security Monitoring Dashboard**
Built a 3-panel dashboard — "Security Monitoring — Divine" — showing failed requests over time, top attacking IPs by volume, and geographic origin of suspicious traffic. This is what a SOC analyst looks at every morning.

**Custom Brute Force Detection Rule**
Created a Threshold rule in Elastic Security: same IP generates 10+ failed requests within a 5-minute window triggers a High severity alert. Rule fired against real log data. Alert triaged and documented.

**Incident Triage Report**
Formal one-page report documenting the fired alert: source IP, timestamps, volume, analysis, evidence, and remediation recommendations. Structured in the format used by real security operations teams.

---

## The Thesis Connection

My undergraduate thesis (*Cryptanalysis of Cryptographic Algorithms in Nigerian Banking Security*, University of Debrecen, 2025) found that BCrypt's default cost factor of 10 is insufficient against modern brute-force attacks and recommended cost factor 12 as the minimum.

This exercise closes the loop on that finding:

**Research said:** An attacker can attempt hundreds of passwords per second against a weak hash.

**Vault-API implemented:** BCrypt cost factor 12 — each check takes ~400ms, slowing automated attacks from milliseconds to minutes.

**ThreatLens detects:** The SIEM fires a High severity alert after the 10th failed attempt in 5 minutes — catching the attack before it succeeds.

The research identified the vulnerability. The API implements the defense. The SIEM catches what gets through.

---

## Key Queries

```kql
# Filter failed requests
response:404

# Filter by geographic origin
geo.dest:"NG"

# Combined failed auth filter
response:404 OR response:503
```

```esql
# Top IPs by failed request volume
FROM kibana_sample_data_logs
| WHERE response == "404"
| STATS count = COUNT() BY clientip
| SORT count desc
| LIMIT 10
```

---

## Detection Rule

| Field | Value |
|-------|-------|
| Rule type | Threshold |
| Index | logs-* |
| Query | response:404 |
| Threshold field | clientip |
| Threshold value | 10 |
| Time window | 5 minutes |
| Severity | High |
| Rule name | Brute Force Detection — High Volume 404s |

---

## Deliverables

- Screenshots of Discover queries and filtered results
- Top suspicious IPs with request counts
- 3-panel security monitoring dashboard
- Custom detection rule with fired alert
- **[Incident Triage Report PDF](./Incident_Triage_Report_BruteForce_Divine.pdf)**

---

## Related Work

**[Vault-API](https://github.com/Divinekk/Vault-API)**
Secure banking backend implementing thesis findings. BCrypt cost 12, AES-256-GCM encryption, BOLA prevention.

**[Nigerian-Fintech-Security-Analysis](https://github.com/Divinekk/Nigerian-Fintech-Security-Analysis)**
Research repository with full thesis, case studies, and vulnerability analysis.

**[Blog Series](https://medium.com/@divine.ogbonna.chisom)**
API Security in Nigerian Fintech — three-part published series.

---

## Contact

Divine Ogbonna
[GitHub](https://github.com/Divinekk) | [LinkedIn](https://www.linkedin.com/in/ogbonna-divine-a81453242/) | [Medium](https://medium.com/@divine.ogbonna.chisom)
