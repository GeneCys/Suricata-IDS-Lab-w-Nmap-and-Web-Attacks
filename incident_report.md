# Incident Report — Exercise 4: Suricata IDS Detection Lab

## Summary

During routine network monitoring on the IDS server (192.168.56.101), Suricata generated multiple alerts related to port scanning and suspicious HTTP requests originating from a client system (192.168.56.102).
This incident report documents the activity, severity, and recommended containment actions.

---

## Incident Details

**Date:** 14.11.2025
**Prepared by:** Gene Crittenden
**Role:** SOC Analyst (Lab Exercise)
**Detected by:** Suricata IDS
**Detection Source:** `/var/log/suricata/eve.json`
**Environment:** VirtualBox host-only network
**IDS Monitored Interface:** `enp0s3`

---

## Timeline of Events

| Time (AEST) | Event Description |
|-------------|------------------|
| 00:00 | Suricata started monitoring on interface `enp0s3` |
| 00:02 | ICMP traffic observed from 192.168.56.102 to 192.168.56.101 |
| 00:03 | Nmap SYN scan initiated from the client VM |
| 00:04 | Suricata triggered multiple ET SCAN alerts |
| 00:07 | Malicious HTTP requests sent to the Nginx web server |
| 00:08 | Suricata generated ET WEB_ATTACK alerts |
| 00:10 | Operator reviewed alerts and Wireshark packet captures |

---

## Observed Alerts

### 1. **ET SCAN NMAP SYN Scan**
Suricata captured SYN packets without ACK responses, consistent with an Nmap SYN stealth scan.

**Example log entry:**
````
“alert”: {
“signature_id”: 2001219,
“signature”: “ET SCAN NMAP SYN Scan”,
“severity”: 2,
“category”: “Potentially Bad Traffic”
}
````


**Source IP:** 192.168.56.102
**Destination IP:** 192.168.56.101

---

### 2. **ET SCAN NMAP OS Detection**
Generated during the `-A` aggressive scan.

**Example log entry:**
````
“alert”: {
“signature”: “ET SCAN Nmap OS Detection Probe”,
“severity”: 2
}
````

---

### 3. **ET WEB_ATTACK SQL Injection Attempt**
Triggered by a crafted HTTP query string containing `' OR 1=1 --`.

**Traffic example:**
````
GET /?id=’ OR 1=1 – HTTP/1.1
````

---

### 4. **ET WEB_ATTACK Directory Traversal Attempt**
Triggered by:
````
curl http://192.168.56.101/../../etc/passwd
````

Suricata logged this as an encoded or direct traversal attempt.

---

## Impact Assessment

| Category | Assessment |
|----------|------------|
| **Confidentiality** | Low — lab environment, no sensitive data |
| **Integrity** | Low — no successful exploitation observed |
| **Availability** | Low — no services disrupted |
| **Overall Severity** | **Low** (Expected lab activity) |

All activity originated from an internal VM used for security testing. There was no real compromise.

---

## Root Cause

The client VM intentionally generated simulated attack traffic using:

- `nmap -sS -Pn`
- `nmap -A`
- SQL injection payload via `curl`
- Directory traversal attempts

Suricata correctly detected and logged all relevant activities.

---

## Containment & Mitigation

- No containment necessary (controlled lab).
- Suricata configuration validated to monitor the correct interface (`enp0s3`).
- YAML configuration corrected by disabling unused `pcap` capture engine.
- Future exercises should also test rule tuning and alert suppression.

---

## Recommendations

1. Maintain Suricata monitoring only on the intended network interface.
2. Periodically review rule updates from the Emerging Threats ruleset.
3. Extend the lab to include:
- Firewall log correlation
- SIEM ingestion (Elastic or Splunk)
- Suricata thresholding and rule tuning
4. Capture additional packet traces for documentation and analysis.

---

## Supporting Files
1. Wireshark packet capture filter screenshots
  - wireshark_filters_1.png
  - wireshark_filters_2.png
  - wireshark_filters_3.png
  - wireshark_filters_4.png
  - wireshark_filters_5.png
  - wireshark_filters_6.png
2. Extracted Suricata alert logs
  - eve.json
3. Text converted Wireshark pcap logs for GitHub compatibility
  - suspicious_scans.txt
4. Nmap scan screenshots
  - nmap_scans_1.png
  - nmap_scans_2.png

---

## End of Report

