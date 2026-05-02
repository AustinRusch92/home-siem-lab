# Home Lab SIEM — Threat Detection & Incident Response

A personal security lab built to simulate, detect, and document real-world attack scenarios using an open-source SIEM stack. This project reflects hands-on work in log analysis, alert triage, and incident documentation — core skills for a SOC analyst role.

---

## Lab Architecture

| Component | Tool | Role |
|---|
| SIEM & EDR | Wazuh 4.14 | Detection, correlation, alerting |
| Log storage | OpenSearch | Indexing and search |
| Dashboard | Wazuh Dashboard | Visualization and triage |
| Endpoint (monitored) | Host OS via Wazuh agent | Real telemetry source |
| Attack simulation | Kali Linux VM | Controlled threat generation |
| Hypervisor | VirtualBox | Single-laptop VM hosting |

**Host constraints:** Entire lab runs on a single laptop using VirtualBox. Wazuh deployed on Ubuntu 22.04 (4 GB RAM). Kali Linux attack VM on an isolated host-only network segment.

---

## Detection Scenarios

Each scenario maps to a MITRE ATT&CK technique and includes a full incident report with timeline, IOCs, and remediation recommendations.

| # | Scenario | MITRE Technique | Report |
|----|
| 1 | SSH brute force via Hydra | T1110.001 — Brute Force: Password Guessing | [IR-001](./incident-reports/IR-001-ssh-brute-force.md) |
| 2 | Network reconnaissance via Nmap | T1046 — Network Service Discovery | [IR-002](./incident-reports/IR-002-nmap-recon.md) |
| 3 | Local privilege escalation | T1548 — Abuse Elevation Control Mechanism | [IR-003](./incident-reports/IR-003-privilege-escalation.md) |
| 4 | File integrity monitoring alert | T1565 — Data Manipulation | [IR-004](./incident-reports/IR-004-fim-alert.md) |

---

## Skills Demonstrated

- SIEM deployment and configuration (Wazuh)
- Agent-based endpoint telemetry (Linux and Windows)
- Alert triage and log analysis
- Attack simulation and detection validation
- Incident documentation and reporting
- MITRE ATT&CK framework mapping

---

## Background

CompTIA Security+ certified Cybersecurity student with Tier 2 Desktop Support experience. This lab was built to bridge the gap between theoretical security knowledge and hands-on detection and response work. Each incident report is written to reflect real SOC documentation standards — timeline, IOCs, root cause, and remediation — not just screenshots of alerts firing.

---

## Repository Structure

```
home-siem-lab/
├── README.md
├── architecture/
│   └── lab-overview.md        # Detailed architecture and design decisions
├── detections/
│   ├── brute-force-ssh.md     # Detection rule and tuning notes
│   ├── port-scan-detection.md
│   ├── privilege-escalation.md
│   └── file-integrity.md
└── incident-reports/
    ├── IR-001-ssh-brute-force.md
    ├── IR-002-nmap-recon.md
    ├── IR-003-privilege-escalation.md
    └── IR-004-fim-alert.md
```

---

## Tools & Technologies

`Wazuh` `OpenSearch` `Ubuntu 22.04` `Kali Linux` `VirtualBox` `Sysmon` `Hydra` `Nmap` `MITRE ATT&CK`
