# Detection Recommendation Model

This model provides a structured approach for developing detection recommendations. It considers the operational environment, the protocol's behavioral characteristics, baseline activity patterns, and operational impacts to support practical, maintainable detections.

### Model

1. **Environmental Context**: *What operational environment generated the telemetry?*
    - Environmental context influences expected behavior,
threshold selection, and alert severity.

2. **Activity Coverage**: *What aspect of the activity is being monitored?*
    - Examples include activity volume, activity rate, behavioral sequence, or protocol interaction depth.

3. **Protocol Behavior**: *What protocol behavior should be detected?*
    - Protocol states, commands, or interaction patterns.

4. **Behavioral Segmentation**: *How activity is grouped?*
    - Establishing baselines and differentiating between routine,
elevated, and abnormal behavior.

5. **Operational Considerations**:
    - How noisy is the detection?
    - Is suppression required?
    - How often should it fire?
    - What action should analysts take?
    - What severity should be assigned?

### e.g.
1. Environmental Context: **Honeypot**
2. Activity Coverage: **Volume and Rate**
3. Protocol Behavior: **SMB Negotiation Command**
4. Behavioral Segmentation: **Based on activity behavior**
5. Operational Considerations: **Suppression for rule1**
## Examples
- [SMB Threshold Based Detection Rules](../../case-study/smb-traffic-spike-honeypot/phase5/phase5A.md#detection-rule-recommendation)
- [SMB Behavioral Detection Rules](../../case-study/smb-traffic-spike-honeypot/phase5/phase5B.md#detection-rule-recommendation)










