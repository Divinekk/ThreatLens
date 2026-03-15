# ThreatLens
A documented hands-on exercise using Elastic Security (ELK Stack) to perform real log analysis, identify attack patterns, build a security monitoring dashboard, create custom detection rules, and write a formal incident triage report.

---

## What I Did

### 1. Log Ingestion and Analysis
- Loaded Apache-style web server logs into Elasticsearch via Elastic Cloud
- Used Kibana Discover to query and filter 1,600+ log entries
- Identified response code patterns: 200 (success), 404 (not found), 503 (server error)
- Filtered Nigerian-origin traffic using geo.dest field
- Identified top source IPs generating repeated error responses

### 2. Attack Pattern Identification
- Searched for repeated 404 responses indicating directory enumeration or endpoint probing
- Aggregated requests by source IP to identify high-volume suspicious actors
- Explored Elastic Security Overview dashboard — network events, anomalies, alert panels
- Enabled Elastic prebuilt detection rules for web attack patterns

### 3. Security Monitoring Dashboard
Built a 3-panel dashboard titled **"Security Monitoring — Divine"** containing:
- **Panel 1:** Failed auth attempts over time (bar chart by timestamp)
- **Panel 2:** Top source IPs for failed requests (treemap by clientip)
- **Panel 3:** Geographic origin of suspicious traffic (map visualization)

### 4. Custom Brute Force Detection Rule
Created a custom Threshold detection rule in Elastic Security:
- **Rule name:** Brute Force Detection — High Volume 404s
- **Logic:** Same IP generates 10+ failed requests within a 5-minute window
- **Severity:** High
- **Result:** Rule fired against sample data, generating a real alert in the Alerts panel

### 5. Incident Triage Report
Wrote a formal one-page incident triage report based on the fired alert:
- Alert triggered, source IP, timestamps, volume, analysis, recommendation
- Structured in the format used by real SOC teams
- Saved as: `Incident_Triage_Report_BruteForce_Divine.pdf`

---

## Connection to Thesis Research

My undergraduate thesis (*Cryptanalysis of Cryptographic Algorithms in Nigerian Banking Security*, University of Debrecen, 2025) recommended BCrypt cost factor 12 for password hashing specifically because of brute force risk.

This exercise closes the loop on that research:

- **Thesis finding:** BCrypt cost 10 is insufficient. An attacker can attempt hundreds of passwords per second against a weak hash.
- **Vault-API implementation:** BCrypt cost 12 — each hash check takes ~400ms, making automated attacks slow.
- **This exercise:** The SIEM fires an alert after 10 failed attempts in 5 minutes, detecting the attack before it succeeds.

Research identified the vulnerability. Vault-API implements the defense. This exercise demonstrates the detection layer.

---

## Tools Used

| Tool | Purpose |
|------|---------|
| Elastic Cloud | Hosted Elasticsearch + Kibana environment |
| Kibana Discover | Log querying and filtering |
| Elastic Security | Alert management and detection rules |
| Kibana Lens | Dashboard and visualization building |
| Kibana Maps | Geographic traffic visualization |

---

## Key Queries Used

```
# Filter failed requests
response:404

# Filter by geographic origin
geo.dest:"NG"

# Top IPs by request volume (ES|QL)
FROM kibana_sample_data_logs 
| WHERE response == "404" 
| STATS count = COUNT() BY clientip 
| SORT count desc 
| LIMIT 10
```

---

## Incident Triage Report

The full triage report is included in this repository as:  
`Incident_Triage_Report_BruteForce_Divine.pdf`

It documents the brute force pattern detected, source IP analysis, evidence from Kibana screenshots, and remediation recommendations.

---

## Related Projects

- **Vault-API** — Spring Boot secure banking backend implementing research findings: [github.com/Divinekk/Vault-API](https://github.com/Divinekk/Vault-API)
- **Nigerian-Fintech-Security-Analysis** — Research repository with thesis and case studies: [github.com/Divinekk/Nigerian-Fintech-Security-Analysis](https://github.com/Divinekk/Nigerian-Fintech-Security-Analysis)
- **Blog Series** — API Security in Nigerian Fintech: [medium.com/@divine.ogbonna.chisom](https://medium.com/@divine.ogbonna.chisom)

---
