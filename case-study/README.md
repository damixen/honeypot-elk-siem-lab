# Case Studies — Security Monitoring & Detection Lab

This directory contains security telemetry and detection engineering case studies derived from a T-Pot honeypot-based monitoring environment.

Each case study documents observed network activity, analysis methodology, and supporting telemetry collected from a live honeypot deployment.

The goal of this section is to provide structured, repeatable examples of:

- security event analysis
- attack pattern identification
- log correlation across ELK stack
- infrastructure-aware investigation workflows

## Scope

Each case study may include:

- honeypot telemetry (network interaction logs)
- Elasticsearch/Kibana queries and visualizations
- behavioral analysis of observed activity
- correlation with system or container metrics (Metricbeat)
- classification of scanning, brute-force, or exploitation patterns

All events are interpreted as observed network activity and telemetry signals rather than confirmed malicious attribution.

## Analysis Environment Context

Case studies are derived from a dual-environment architecture:

- **Production environment**: live T-Pot honeypot deployment collecting internet-facing telemetry  
- **Investigation environment**: locally restored Elasticsearch snapshots used for offline analysis and dashboard development  


This separation enables safe investigation without impacting live telemetry collection.

## Available Case Studies
### [SMB Traffic Spike Investigation](smb-traffic-spike-honeypot)

Analysis of anomalous SMB-related traffic patterns observed in honeypot telemetry, including scan behavior and repeated connection attempts.

## Methodology

Case studies generally follow this workflow:

1. Identify anomaly or notable traffic pattern in Kibana dashboards
2. Validate raw telemetry in Elasticsearch indices
3. Correlate with system or container metrics where applicable
4. Classify behavior (e.g., scanning, brute-force, service probing)
5. Document findings with supporting queries and visualizations

## Technologies Used
- T-Pot Honeypot Platform
- Elasticsearch / Logstash / Kibana (ELK stack)
- Metricbeat
- Docker
- Linux (Ubuntu)
- Snapshot-based Elasticsearch workflows

## Notes on Interpretation

All recorded events represent:

- automated scanning activity
- service probing behavior
- authentication attempts or exploitation attempts

They are analyzed as telemetry signals, not confirmed attacker attribution.

Some screenshots retain original T-Pot / Kibana labeling where “attack” is used as a generic field for observed network activity. In this report, these events are interpreted as “activity” (e.g., scans and probes) rather than confirmed malicious attacks or attribution of intent.

## Navigation

Start with any case study folder to view:

- analysis context
- supporting queries
- findings and interpretation
- related visualizations 
