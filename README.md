# Suricata IDS Lab — Network Monitoring & Threat Detection

**SOC-style exercise** demonstrating host-based intrusion detection using Suricata IDS, simulated attacks, and correlation with network traffic captures.

- **Author:** Gene Crittenden
- **Date:** 16 November 2025
- **IDS Server VM:** 192.168.56.101
- **Client/Attack VM:** 192.168.56.102

---

## Overview

This lab demonstrates how to deploy and configure **Suricata IDS** on a **Linux** VM to detect network attacks generated from a client VM. Participants will:
- Monitor traffic in a host-only network
- Generate Nmap scans and web-based attacks
- Capture and analyze Suricata alerts (eve.json)
- Correlate alerts with packet-level activity in Wireshark

**Learning Objectives**
- Install and configure Suricata on a Linux VM
- Monitor traffic on a host-only network interface
- Generate network scans and web attacks from a client VM
- Capture and analyze Suricata alerts (`eve.json`)
- Understand mapping between detected network events and SOC alerts

**Why This Lab Matters:**
Hands-on practice with IDS systems provides critical SOC skills in monitoring, detecting, and documenting suspicious network activity — essential for threat detection and incident response roles.

---

## Lab Setup

Two VMs connected via a host-only VirtualBox network.
| VM                 | IP Address     | Installed Tools | Role                              |
| ------------------ | -------------- | --------------- | --------------------------------- |
| IDS Server         | 192.168.56.101 | Suricata, Nginx | Monitor traffic & generate alerts |
| Client / Attack VM | 192.168.56.102 | Nmap, curl      | Simulate network attacks          |

---

## Step 1. Configure Suricata

1. Edit `/etc/suricata/suricata.yaml` to set the host-only interface:
```yaml
af-packet:
- interface: enp0s3
  threads: auto
  cluster-id: 99
  cluster-type: cluster_flow
  defrag: yes
```
2. Comment out or remove any `.pcap:` blocks.
3. Restart Suricata
```bash
sudo systemctl restart suricata
sudo systemctl status suricata
```

---

## Step 2. Generate Traffic and Attacks

From the `client` VM, execute network scans and web-based attacks:
<details> <summary>Attack & Scan Commands</summary>
  
```bash
# Nmap SYN scan
sudo nmap -sS -Pn 192.168.56.101

# Aggressive scan with OS detection
sudo nmap -A 192.168.56.101

# Web attack simulation
curl "http://192.168.56.101/?id=' OR 1=1 --"
curl http://192.168.56.101/../../etc/passwd
```
</details>

On the server VM, Suricata will log alerts in `/var/log/suricata/eve.json`.

---

## Step 3. Monitor Suricata Alerts

View and analyze alerts:
<details> <summary>Alert Commands</summary>

```bash
sudo tail -f /var/log/suricata/eve.json
# OR
sudo less /var/log/suricata/eve.json
# Search for:
# "alert"
# "ET SCAN"
# "ET WEB_ATTACK"
```
</details>

Correlate alerts with timestamps and source IPs for SOC documentation.

---

## Step 4. Wireshark Correlation

Capture traffic between the client and server VM to verify Suricata detections:
<details> <summary>Wireshark Filters</summary>

```bash
icmp
tcp.flags.syn == 1 && tcp.flags.ack == 0   # Nmap SYN scans
http && ip.dst == 192.168.56.101          # Web requests
ip.addr == 192.168.56.101 || ip.addr == 192.168.56.102  # General traffic
tcp.port == 443                            # SSL/TLS traffic
```
</details>

Use packet-level analysis to confirm Suricata detection accuracy.

---

## Step 5 — SOC Response

In a real-world environment when faced with this scenario, A SOC analyst would:

<details><summary>SOC Response</summary>
1. Validate Alerts
- Review eve.json and capture logs to confirm scan or attack origin.
2. Assess Risk
- Determine whether the activity is benign testing or a potential intrusion.
3. Mitigate
- Adjust firewall rules or block malicious IPs if unauthorized.
4. Monitor & Alert
- Configure IDS/alerting rules to detect repeated or similar attacks.
5. Document Findings
- Record detected activity, timestamps, source IPs, and correlate with logs/screenshots.
</details>

**Outcome:**
SOC gains visibility into scanning and attack behavior, validates IDS coverage, and generates professional incident documentation.

---

## GRC Mapping

This section maps the lab activities to common governance, risk, and compliance (GRC) frameworks, demonstrating how technical SOC operations support organizational compliance and policy requirements.
<details><summary>GRC Mapping</summary>

| Framework                 | Control / Requirement                       | Lab Activity                                                               | Purpose                                                                                |
| ------------------------- | ------------------------------------------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| **NIST SP 800-53 Rev. 5** | **SI-4: Information System Monitoring**     | Suricata monitoring of host traffic, alerting on scans and attacks         | Detect reconnaissance and potential intrusion activity to maintain system integrity    |
| **ISO 27001:2022**        | **A.12.4: Logging and Monitoring**          | Capture and analysis of network traffic, storing alerts in `eve.json`      | Ensure logging and continuous monitoring of network security events                    |
| **CIS Controls v8**       | **8.1: Audit Logging**                      | Maintain Suricata logs and correlate with Wireshark captures               | Track scanning and attack activity for auditing and compliance evidence                |
| **PCI DSS v4.0**          | **10.2: Implement Audit Trails**            | Alert correlation and recording of source IPs, timestamps, and event types | Provide traceable audit logs for security events impacting cardholder data environment |
| **MITRE ATT&CK**          | **Recon: T1046 (Network Service Scanning)** | Nmap SYN scans detected by Suricata                                        | Identify adversary techniques to inform detection and mitigation strategies            |
</details>

**Purpose of Mapping:**
- Demonstrates that technical controls (Suricata IDS + monitoring) support compliance requirements.
- Provides documentation evidence for audits and regulatory reporting.
- Links SOC detection activity to standardized GRC frameworks, strengthening organizational security posture.

---

## Skills Demonstrated
- Host-based IDS deployment and configuration (Suricata)
- Network traffic capture & analysis (Wireshark)
- Detection of reconnaissance and web-based attacks
- Incident analysis & alert correlation
- SOC workflow: Detect → Analyze → Respond → Document

---

## Lessons Learned
- Suricata alerts must be correlated with packet-level captures to confirm activity.
- Nmap scanning and web attacks generate identifiable network patterns.
- Proper SOC monitoring requires both IDS alerts and contextual analysis.
- Documenting each step ensures reproducibility and professional-grade reporting.
- Mapping events to GRC frameworks supports compliance and auditing.

--- 

## Supporting Files
**Reports**
- [Incident Report](./incident_report.md)
- [GRC Report](./grc_report.md)

[Logs](./logs)
- Nmap Scans & Wrieshark Packet Captures
  - _The original .pcapng file is kept offline and available upon request._

[Screenshots](./screenshots)
- Nmap Scans & Wireshark Filters

---

**End of Report**
