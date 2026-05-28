
## From Spike Detection to Behavioral Correlation: Investigating Concentrated SMB Activity

### Investigation Status
Phase 3 Complete — Ongoing Investigation

## Summary
This is a case report investigating a spike in activities against the T-Pot honeypot on a cloud platform. This investigation began by selecting anomalous activity from honeypot telemetry to explore how the SOC/detection/threat hunting workflow operate in practice. This investigation will be expanded in future phases.

## Investigation Objective
The objective of this investigation was to determine whether the observed traffic spike represented broad internet scanning activity, automated exploitation attempts, or coordinated targeting against a specific exposed service.

### Environment
- T-Pot
- DigitalOcean (US-based region, Northern California)
- ELK stack

### Data
T-Pot honeypot logs from May 2nd, 2026 to May 14th, 2026

## Phases
- [Phase 1 - Initial Detection & Triage](phase1/README.md)
- [Phase 2 - Traffic Characterization](phase2/README.md)
- [Phase 3 - Historical Correlation](phase3/README.md)
- [Phase 4 - Thread Enrichment](phase4/README.md)
- [Phase 5 - Detection Recommendations](phase5/README.md)
- [Phase 6 - Conclusions](phase6/README.md)

## Screenshots
![sc1](phase1/screenshots/init-range.png)
![sc2](phase2/screenshots/activity-port-stats.png)

## Skills Demonstrated
- ELK Stack
- Kibana Visualization
- Threat Hunting
- Historical Correlation
- Network Telemetry Analysis
- Honeypot Operations
- SIEM Investigation Workflow