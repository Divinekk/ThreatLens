# ThreatLens

Hands-on SIEM practice using Elastic Security on Elastic Cloud. Real log analysis, custom detection rules, and a formal incident triage report.

> "You cannot defend what you cannot detect. This is the detection layer."

---

## About This Project

Real-world security operations workflow using the ELK Stack (Elasticsearch, Kibana, Elastic Security). Ingested web server logs, identified attack patterns, built a three-panel security monitoring dashboard, created a custom brute force detection rule, and produced a formal incident triage report.

This exercise is the third layer of a connected body of work:

- **Thesis** — identified the cryptographic vulnerabilities in Nigerian banking systems
- **Vault-API** — implemented the defenses in code
- **ThreatLens** — built the detection layer to catch attacks in progress

**[Read the Incident Triage Report](Incident_Triage_Report_BruteForce_Divine..pdf)**

---

## What I Built

**Log Analysis**
Ingested Apache-style web server logs into Elasticsearch via Elastic Cloud. Queried 1,619 log entries using Kibana Discover. Identified response code distributions across 200, 404, and 503 responses. Filtered Nigerian-origin traffic using the geo.dest field. Isolated high-frequency error patterns by source IP using field statistics.

**Attack Pattern Detection**
Identified repeated 404 responses indicating directory enumeration and endpoint probing. Used ES|QL to aggregate requests by source IP and surface the highest-volume suspicious actors. The sample dataset did not contain 401 authentication failures — 404 responses were used as the failed request signal, which in a real environment would indicate reconnaissance and endpoint scanning (OWASP API9).

**Security Monitoring Dashboard**
Built a three-panel dashboard titled "Security Monitoring — Divine" containing:
- Panel 1: Failed requests over time — bar chart by @timestamp showing attack volume trends
- Panel 2: Top source IPs — pie chart showing which IPs generated the most failed requests
- Panel 3: Geographic origin — map visualization showing where suspicious traffic originated

**Custom Brute Force Detection Rule**
Created a Threshold detection rule in Elastic Security:
- Rule fires when the same IP address generates 2 or more requests within a 5-minute window
- Severity: High, Risk score: 73
- Rule executed successfully and is enabled
- Note: Elastic Cloud free tier separates the Security project index space from the Analytics sample data index, so alerts were not generated against the sample dataset. The rule configuration, execution log, and detection logic are documented in screenshots.

**Incident Triage Report**
Formal one-page report documenting findings from the dashboard analysis: suspicious IP patterns, request volume, timeline, analysis of likely intent, and remediation recommendations. Structured in the format used by real security operations teams.

---

## The Thesis Connection

My undergraduate thesis (*Cryptanalysis of Cryptographic Algorithms in Nigerian Banking Security*, University of Debrecen, 2025) found that BCrypt's default cost factor of 10 is insufficient against modern brute-force attacks and recommended cost factor 12 as the practical minimum.

This exercise closes the loop on that finding:

**Research said:** An attacker can attempt hundreds of passwords per second against a weak hash. BCrypt cost 10 offers insufficient resistance to modern hardware.

**Vault-API implemented:** BCrypt cost factor 12 — each password check takes approximately 400ms, slowing automated attacks from milliseconds to minutes.

**ThreatLens detects:** The SIEM threshold rule fires a High severity alert after repeated failed requests from the same IP — catching the attack pattern before it can succeed against even a hardened hash.

The research identified the vulnerability. The API implements the defense. The SIEM catches what gets through.

---

## Key Queries

```kql
# Filter failed requests — Classic KQL mode
response:404

# Filter by geographic origin
geo.dest:"NG"

# Combined failed request filter
response:404 OR response:503
```

```esql
# Top IPs by failed request volume — ES|QL mode
FROM kibana_sample_data_logs
| WHERE response == "404"
| STATS count = COUNT() BY clientip
| SORT count desc
| LIMIT 10

# Response code distribution
FROM kibana_sample_data_logs
| STATS count = COUNT() BY response
| SORT count desc
```

---

## Detection Rule

| Field | Value |
|-------|-------|
| Rule name | Brute Force Detection — High Volume 404s |
| Rule type | Threshold |
| Index | logs-* |
| Custom query | * (match all events) |
| Threshold | All results >= 2 |
| Time window | 5 minutes |
| Severity | High |
| Risk score | 73 |
| Schedule | Runs every 5 minutes |

---

## Screenshots

All evidence is documented in the `screenshots/` folder:

- `01_discover_404_filter.png` — Kibana Discover filtered to 404 responses, 83 results
- `02_suspicious_ips.png` — Field statistics showing top source IPs by request count
- `03_dashboard.png` — Three-panel security monitoring dashboard
- `04_detection_rule.png` — Configured brute force detection rule, enabled, High severity

---

## Deliverables

- Documented log queries and filtered results
- Top suspicious IPs with request counts
- Three-panel security monitoring dashboard
- Custom detection rule configured and enabled
- **[Incident Triage Report PDF](./Incident_Triage_Report_BruteForce_Divine.pdf)**

---

## Honest Limitations

- Sample dataset does not contain authentication failures (401 responses) — 404 was used as the attack signal
- Elastic Cloud free tier separates Security and Analytics data spaces — alerts did not fire against sample data
- Geographic data reflects Elastic's sample dataset, not real Nigerian traffic
- This is a learning exercise, not a production deployment

---

## Related Work

**[Vault-API](https://github.com/Divinekk/Vault-API)**
Secure banking backend implementing thesis findings. BCrypt cost 12, AES-256-GCM encryption, BOLA prevention.

**[SentinelAPI](https://github.com/Divinekk/SentinelAPI)**
Spring Boot tool that detects OWASP API attack patterns in logs and generates structured incident reports. The engineering equivalent of what ThreatLens does through Kibana.

**[Nigerian-Fintech-Security-Analysis](https://github.com/Divinekk/Nigerian-Fintech-Security-Analysis)**
Research repository with full thesis, case studies, and vulnerability analysis.

**[Blog Series](https://medium.com/@divine.ogbonna.chisom)**
API Security in Nigerian Fintech — three-part published series.

---

## Contact

Divine Ogbonna
[GitHub](https://github.com/Divinekk) | [LinkedIn](https://www.linkedin.com/in/ogbonna-divine-a81453242/) | [Medium](https://medium.com/@divine.ogbonna.chisom)
