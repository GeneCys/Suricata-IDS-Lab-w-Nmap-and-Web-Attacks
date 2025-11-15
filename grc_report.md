# Governance, Risk, and Compliance (GRC) Report
Suricata IDS Monitoring & Detection

## 1. Executive Summary

This GRC report documents the security monitoring exercise conducted using Suricata IDS on a host-only virtual network.
The purpose of the exercise was to validate IDS effectiveness, ensure alignment with governance requirements, assess risks associated with detected activity, and confirm compliance with established security policies.

Suricata successfully detected multiple categories of malicious traffic—port scanning, OS fingerprinting, SQL injection attempts, and directory traversal attempts—originating from a controlled client VM.
All activity occurred in an isolated test environment, with no real operational impact.

---

## 2. Objectives & Scope

### **Objectives**
- Validate Suricata IDS rule effectiveness.
- Ensure the IDS is correctly monitoring the approved network interface.
- Evaluate how detected events align with risk thresholds and policy compliance.
- Produce documentation suitable for auditors or security governance frameworks.

### **Scope**
- Systems in scope:
- **IDS Server:** 192.168.56.101
- **Client/Test Machine:** 192.168.56.102
- VirtualBox host-only network via **enp0s3**
- Relevant controls assessed:
  - Network monitoring
  - Logging and alerting
  - Detection rule coverage
  - Configuration governance

---

## 3. Governance Alignment

### **Relevant Governance Principles**
This exercise aligns with common governance frameworks such as:

- **NIST CSF – Detect (DE):**
- DE.AE-1 (Anomalies and events are detected)
- DE.CM-1 (Network monitoring is performed)
- DE.DP-4 (Event detection information is communicated)

- **ISO 27001:2022 Controls:**
- **5.23** – Information security event reporting
- **8.16** – Monitoring activities
- **8.24** – Logging and monitoring

### **Governance Outcomes**
- IDS is configured in accordance with security monitoring requirements.
- Traffic monitoring is restricted to the intended interface (`enp0s3`), reducing configuration drift.
- Alerts are logged in a central location (`eve.json`) consistent with governance expectations.

---

## 4. Risk Assessment

### **Threats Observed**
| Activity | Description | Risk Level |
|----------|-------------|------------|
| **SYN & OS scanning** | Identifies open ports and potential exploits | Medium |
| **SQL injection attempt** | Tries to manipulate backend queries | Medium |
| **Directory traversal attempt** | Attempts unauthorized file access | Medium |
| **HTTP probing** | Enum of service responses | Low |

### **Risk Context**
- All activity originated from a controlled lab system.
- No sensitive data, operational systems, or real users were affected.
- Risk remains **Low** due to isolation and intentional nature of testing.

### **Residual Risk:** **Low**

---

## 5. Compliance Review

### **Compliance Requirements Evaluated**
| Requirement | Status | Notes |
|-------------|--------|-------|
| IDS must monitor approved interfaces | **Compliant** | Validated on `enp0s3` |
| Alerts must generate log entries | **Compliant** | Verified through `eve.json` |
| Signatures must match common attack behaviours | **Compliant** | ET SCAN, ET WEB_ATTACK triggered |
| Logging must be accessible & auditable | **Compliant** | Logs reviewed using grep/less |
| Unused capture methods must not introduce configuration errors | **Compliant** | `pcap` section removed or commented |

### **Compliance Conclusion**
The system meets all compliance checks for this exercise. No configuration issues remain.

---

## 6. Control Effectiveness

### **Strengths**
- **Detection:** Suricata detected expected malicious behaviours with no false negatives.
- **Logging:** All alerts appeared correctly in `eve.json`.
- **Rule Coverage:** Emerging Threats ruleset successfully flagged multiple signatures.
- **Configuration Governance:** Misalignment (incorrect interface and pcap engine conflict) was resolved.

### **Weaknesses**
- Initial YAML misconfiguration prevented Suricata from starting correctly.
- Logs were initially unreadable due to binary formatting issues.
- Future exercises should include log forwarding (to SIEM) for improved auditability.

---

## 7. Recommendations

### **Short-Term**
- Continue using host-only network interface for consistent isolation.
- Add timestamps and tagging to Suricata logs for easier audit correlation.
- Introduce automated configuration validation (e.g., `suricatactl configtest`).

### **Medium-Term**
- Integrate logs into a SIEM (Elastic, Splunk, or Wazuh).
- Implement alert triage workflows to align with SOC procedures.
- Expand ruleset testing (e.g., malware C2, brute force, TLS anomalies).

### **Long-Term**
- Develop organisation-wide IDS governance policy.
- Conduct periodic configuration reviews to reduce drift.
- Implement threat-hunting playbooks that reference Suricata alerts.

---

## 8. Conclusion

This exercise demonstrates that Suricata is functioning effectively as an IDS within the lab environment, with strong alignment to governance principles, low operational risk, and full compliance with monitoring requirements.

The system is ready for further testing involving SIEM integration, tuning, and advanced detection scenarios.

---

**Prepared by:** Gene Crittenden
**Role:** Governance, Risk, and Compliance Analyst (Lab Exercise)
