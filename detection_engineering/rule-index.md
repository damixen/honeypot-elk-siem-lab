# Detection Rule Index

This document provides an index of detection rules developed across case studies. Each rule links to its original implementation context, including investigation details, visualizations, and tuning decisions.

---

## SMB Traffic Spike Case Study

### SMB Negotiate Command - 12h Volume Threshold Exceeded
- **Type:** Volume-based detection
- **Behavior:** Excessive SMB negotiation activity over an extended window
- **Case Study Reference:**
  - Phase 5A → [Detection Rule 1](../case-study/smb-traffic-spike-honeypot/phase5/phase5A.md#rule-1-smb-negotiate-command---12h-volume-threshold-exceeded)

- **Purpose:**
  Detect sustained scanning or enumeration behavior targeting SMB services.

---

### SMB Negotiate Command - 5-Minute Activity Rate Threshold Exceeded
- **Type:** Rate-based detection
- **Behavior:** Excessive SMB Negotiate command rate
- **Case Study Reference:**
  - Phase 5A → [Detection Rule 2](../case-study/smb-traffic-spike-honeypot/phase5/phase5A.md#rule-2--smb-negotiate-command---5-minute-activity-rate-threshold-exceeded)
- **Purpose:**
  Detect burst-style scanning behavior or automated reconnaissance activity.

---

### SMB Share Interact Command - 30-min Volume Threshold Exceeded
- **Type:** Volume-based detection
- **Behavior:** Elevated SMB share interaction commands (READ/WRITE/TRANS/DELETE/IOCTL)
- **Case Study Reference:**
  - Phase 5B → [Detection Rule](../case-study/smb-traffic-spike-honeypot/phase5/phase5B.md#smb-share-interact-command---30-min-volume-threshold-exceeded)
- **Purpose:**
  Detect elevated SMB share interaction activity (READ, WRITE, TRANS, DELETE, IOCTL commands)

---

## SSH Brute Force Case Study (Future)

*(Placeholder for future expansion)*

---

## Design Notes

- This index does not contain full detection logic.
- All behavioral context is maintained in case study documentation.
- This file serves as a navigation and discovery layer for detection artifacts.
