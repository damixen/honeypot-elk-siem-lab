
## From Spike Detection to Behavioral Correlation: Investigating Concentrated SMB Activity

### Investigation Status
Phase 3 Complete — Ongoing Investigation

## Summary
This is a case report investigating a spike in activities against the T-Pot honeypot on a cloud platform. This investigation began by selecting anomalous activity from honeypot telemetry to explore how the SOC/detection/threat hunting workflow operate in practice. Then it will be expanded for further investigations.

## Investigation Objective
The objective of this investigation was to determine whether the observed traffic spike represented broad internet scanning activity, automated exploitation attempts, or coordinated targeting against a specific exposed service.

#### About T-Pot
T-Pot is an open-source honeypot platform that supports the deployment and management of multiple honeypots. It's a Docker-based service that manages honeypots and also provides an ELK stack for log collection and visualization. It also supports attack maps and investigative tools such as CyberChef and SpiderFoot.

Honeypots on T-Pot emulate various services with open ports, including HTTP, HTTPS, SSH, FTP, SMB, ADB, and Elasticsearch. The platform is highly customizable and supports selective deployment of honeypot services. Each honeypot generates logs for an activity event, which are collected by Logstash and ingested into Elasticsearch.

#### Note on T-Pot Usage
For my projects, I extended the ELK stack to support Metricbeat to collect System and Docker resources for broader activity correlation. I utilize the existing Kibana visualizations (Dashboards and panels) provided by T-Pot, and I have also created my own visualizations for resource utilization and case study investigation.

#### Note on Terminology
Some screenshots retain the original T-Pot / Kibana labeling where “attack” is used as a generic term for observed network activity. In this report, these events are interpreted as “activity” (e.g., scans and probes) rather than confirmed malicious attacks.


### Environment
- T-Pot
- Digital Ocean in the USA Northern CA Region
- ELK stack

### Data
T-Pot honeypot logs from May 2nd, 2026 to May 14th, 2026

## Phases
- [Phase 1 - Initial Detection & Triage](01-initial-detection-and-triage/report.md)
- [Phase 2 - Traffic Characterization](02-traffic-characterization/report.md)
- [Phase 3 - Historical Correlation](03-historical-correlation/report.md)
- [Phase 4 - Thread Enrichment](04-threat-enrichment/report.md)
- [Phase 5 - Concludions](05-conclusions/report.md)
- [Phase 6 - Detection Recommendations](06-detection-recommendations/report.md)

## Screenshots
![sc1](01-initial-detection-and-triage/screenshots/init-range.png)
![sc2](02-traffic-characterization/screenshots/activity-port-stats.png)

## Skills Demonstrated
- ELK Stack
- Kibana Visualization
- Threat Hunting
- Historical Correlation
- Network Telemetry Analysis
- Honeypot Operations
- SIEM Investigation Workflow