# Investigation Phases

This framework outlines a structured approach for investigating suspicious activities observed in security telemetry. The phases are designed to progressively build understanding of the activity, refine investigative hypotheses, develop detection recommendations, and capture lessons learned for future investigations.

---

## Phase 1: Initial Detection & Triage

Perform an initial assessment of the observed activity to determine scope, potential impact, and areas requiring further investigation.

### Key Points

- Services involved
- Destination ports involved
- Activity trends
    - Spikes
    - Sustained elevation
- Geographic and infrastructure trends
    - Autonomous Systems (AS/ASN)
    - Countries of origin
- Time range of the observed activity

### Objective

Develop an initial understanding of --what happened-- and establish the scope of the investigation.

---

## Phase 2: Traffic Characterization

Investigate the characteristics of the observed traffic to identify patterns and behavioral indicators.

### Key Points

- Distribution of activities by service and destination port
- Source IP-level activity
    - Distribution of activity across source IPs
    - Identification of dominant sources
- AS/ASN-level activity
    - Distribution of activity across infrastructure providers
- Duration of activities
- Temporal characteristics
    - Burst activity
    - Periodic activity
    - Continuous activity

### Initial Behavioral Assessment

- Reconnaissance activity
- Broad or port-wide scanning
- Targeted service scanning
- Prolonged service sessions
- Other notable behavioral patterns

### Objective

Understand --what the activity looks like-- and establish an initial hypothesis regarding its nature.

---

## Phase 3: Historical Correlation

Investigate whether similar activities have been observed previously and identify historical relationships within the telemetry.

### Key Points

- Historical activity associated with:
    - Source IPs
    - AS/ASNs
    - Countries
- First-seen and last-seen observations
- Correlations among source IPs, AS/ASNs, and countries
- Activity timelines
- Availability in OSINT or public indexing services

### Recurrence Analysis

- One-time activity
- Periodic activity
- Persistent activity

### Continued Assessment

- Refinement of investigative hypotheses
- Adjustment of confidence levels
- Identification of new findings

### Objective

Determine --whether the activity is novel, recurring, or persistent--, and refine the overall assessment.

---

## Phase 4: Threat Enrichment

Investigate the contextual meaning of the activity through protocol-level analysis and external intelligence sources.

### Key Points

#### Protocol or Service Context

- Observed commands or protocol states
- Session behavior and flow analysis
- Protocol-specific interaction assessment
- Available threat intelligence or public research

#### Behavioral Assessment

- Service-specific behavior analysis
- Session flow interpretation
- Identification of deeper interaction patterns

### Continued Assessment

- Refinement of investigative hypotheses
- Adjustment of confidence levels
- Identification of new findings

### Objective

Understand --what the activity means-- from a technical and threat perspective.

---

## Phase 5: Detection Engineering Recommendations

Translate investigative findings into actionable detection recommendations.

### Key Points

#### Detection Development

- Define detection behaviors and objectives
- Determine candidate detection opportunities

#### Detection Engineering

- Apply the Rule Creation Model
    - See: [Threshold Rule Creation Model](./threshold_rule_creation_model.md)
- Apply the Detection Recommendation Model
    - See: [Detection Recommendation Model](./detection_recommendation_model.md)

#### Operational Considerations

- Expected alert volume
- Suppression requirements
- Rule execution intervals
- Investigation guidance for analysts

### Continued Assessment

- Refinement of detection recommendations
- Adjustment of confidence levels
- Identification of new findings

### Objective

Determine --how the activity should be monitored or detected operationally--.

---

## Phase 6: Conclusions and Lessons Learned

Summarize investigative findings, detection outcomes, and opportunities for future improvement.

### Investigation Conclusions

- Final assessment of the activity
- Overall confidence level
- Supporting evidence

### Detection Conclusions

- Recommended detections
- Detection limitations
- Operational considerations

### Telemetry Conclusions

- Strengths of the available telemetry
- Observed telemetry gaps
- Opportunities for improved visibility

### Lessons Learned

- Investigative insights
- Detection engineering insights
- Reusable methodologies or frameworks

### Future Work

- Additional analysis opportunities
- Potential rule enhancements
- Dashboard and visualization improvements
- Expansion into related case studies

### Objective

Capture --what was learned-- and preserve knowledge for future investigations.
