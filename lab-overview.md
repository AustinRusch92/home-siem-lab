# Lab Architecture Overview

## Purpose

This lab was built to develop and demonstrate hands-on skills in threat detection, log analysis, and incident response using an open-source SIEM stack. All infrastructure runs on a single laptop using VirtualBox, simulating a constrained but realistic environment similar to small business deployments.

The design prioritizes portfolio depth over breadth — a small number of well-documented detection scenarios rather than a large number of undocumented ones.

---

## Physical Infrastructure

| Component | Specification |
|---|---|
| Host machine | Personal laptop |
| Host OS | Windows 11 |
| RAM | 12 GB total |
| Hypervisor | VirtualBox (free) |
| Network | Host-only adapter (192.168.56.0/24) + NAT |

**RAM allocation:**
- Wazuh SIEM VM: 6 GB
- Kali Linux VM: 2 GB
- Windows 11 host (remaining): ~6 GB

---

## Virtual Machines

### VM 1 — Wazuh SIEM Server
| Setting | Value |
|---|---|
| OS | Ubuntu 26.04 LTS |
| RAM | 4 GB |
| Disk | 50 GB |
| IP | 192.168.56.101 (host-only) |
| Role | SIEM, log indexing, detection, dashboard |

Runs the full Wazuh 4.14 all-in-one stack:
- **Wazuh Manager** — rules engine, alert correlation, agent management
- **Wazuh Indexer** — OpenSearch-based log storage and indexing
- **Wazuh Dashboard** — web UI for alert triage, event search, and visualization

### VM 2 — Kali Linux (Attack Simulation)
| Setting | Value |
|---|---|
| OS | Kali Linux (latest) |
| RAM | 2 GB |
| Disk | 20 GB |
| IP | 192.168.56.102 (host-only) |
| Role | Controlled attack simulation |

Used exclusively for generating attack traffic against lab targets to validate detection rules and produce real alert data for incident reports.

---

## Monitored Endpoint

| Component | Detail |
|---|---|
| Machine | Windows 11 host laptop |
| Agent | Wazuh agent (active) |
| Enrichment | Sysmon with SwiftOnSecurity config |
| Status | Active — streaming telemetry to Wazuh |

Sysmon enriches standard Windows Event Logs with high-fidelity telemetry including full process command lines, parent-child process relationships, network connections, DNS queries, and file creation events. The Wazuh agent ships all of this to the SIEM in near real-time.

---

## Network Diagram

```
┌─────────────────────────────────────────────────────┐
│                   Host Laptop                        │
│                  Windows 11                          │
│              Wazuh Agent + Sysmon                    │
│                                                      │
│  ┌─────────────────┐      ┌───────────────────────┐  │
│  │  VM 1           │      │  VM 2                 │  │
│  │  Wazuh SIEM     │◄─────│  Kali Linux           │  │
│  │  192.168.56.101 │      │  192.168.56.102        │  │
│  │  Ubuntu 26.04   │      │  Attack simulation    │  │
│  └────────┬────────┘      └───────────────────────┘  │
│           │ Host-only network (192.168.56.0/24)       │
└───────────┼─────────────────────────────────────────┘
            │
            ▼ NAT
        Internet
   (Wazuh updates, threat intel)
```

---

## Design Decisions

**Why Wazuh over full ELK stack?**
With 12 GB of RAM split across a host OS and two VMs, the full ELK stack (Elasticsearch + Logstash + Kibana) would leave insufficient memory for stable operation. Wazuh's all-in-one installer bundles a lightweight OpenSearch backend with a built-in detection engine and hundreds of out-of-the-box rules, delivering equivalent visibility at lower resource cost. It is also widely deployed in real SMB environments, making it directly relevant to entry-level SOC work.

**Why a host-only network for the attack VM?**
Isolating Kali to a host-only network segment ensures attack traffic stays entirely within the lab and cannot reach the internet or any external systems. This is standard lab hygiene and mirrors how real security teams isolate test environments.

**Why Sysmon on the Windows host?**
Standard Windows Event Logs lack command-line arguments, parent process context, and network connection details. Sysmon fills these gaps and is increasingly deployed in enterprise environments. Having it running in the lab means the detection scenarios and incident reports reflect real-world telemetry rather than minimal built-in logging.

**Why document a single-laptop constraint?**
Resource constraints are the norm in small business and entry-level environments. Designing a functional SIEM lab within these constraints and documenting the tradeoffs demonstrates practical judgment, not just technical ability.

---

## Tools & Technologies

| Tool | Version | Purpose |
|---|---|---|
| Wazuh | 4.14 | SIEM, EDR, alerting |
| OpenSearch | Bundled with Wazuh | Log indexing and storage |
| Ubuntu | 26.04 LTS | SIEM server OS |
| Sysmon | Latest | Windows endpoint enrichment |
| Kali Linux | Latest | Attack simulation |
| VirtualBox | Latest | Hypervisor |
| Hydra | Bundled with Kali | Brute force simulation |
| Nmap | Bundled with Kali | Network reconnaissance simulation |

---

## Detection Coverage

| Scenario | MITRE Technique | Incident Report |
|---|---|---|
| SSH brute force | T1110.001 | [IR-001](../incident-reports/IR-001-ssh-brute-force.md) |
| Network port scan | T1046 | [IR-002](../incident-reports/IR-002-nmap-recon.md) |
| Privilege escalation | T1548 | [IR-003](../incident-reports/IR-003-privilege-escalation.md) |
| File integrity violation | T1565 | [IR-004](../incident-reports/IR-004-fim-alert.md) |
