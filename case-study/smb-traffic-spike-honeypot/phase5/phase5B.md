# Phase 5B - Session-Level Detection Through SMB Share Interaction Analysis

In [Phase 5A](./phase5A.md), I have covered a hypothesis based on activity volume and rate over a given period, and the detection rules were set heuristically with thresholds. I observed that those rules captured SMB activity spikes.

However, those rules would miss low-volume or low-rate SMB commands, which may indicate deeper interaction with SMB resources, since such interactions tend to occur less frequently and require authentication. Therefore, in this phase, I will focus on SMB's session-level behavior analysis to add another layer of detection coverage to compensate for the previous phase.

## Initial Detection Attempt with Kibana's Event Correlation Rule
Initially, I created a rule in Kibana based on SMB command classification from [Phase 4](../phase4/README.md#smb-command-classification-definition).

For example, Resource Access behavior is:
```
sequence by src_ip, flow_id with maxspan=10m
  [ any where dest_port == 445 and smb.command like "*NEGOTIATE*" ]
  [ any where dest_port == 445 and smb.command like "*SESSION_SETUP*" and (smb.status == "STATUS_SUCCESS" or smb.status == "UNKNOWN_00_0000") ]
  [ any where dest_port == 445 and smb.command like "*TREE_CONNECT*" ]
```

*To track sessions, Suricata's `flow_id` provided a convenient mechanism for grouping related SMB events into a single network session, while `src_ip` helped characterize activity originating from a common source.

Then, the rule was tested with the preview and Kibana Timeline. It successfully showed that matching events and alerts were generated for such a sequence in a preview. However, the issue was that the detection would match any sequence, and I could not use this sequence to quantify such sessions, for detection, or for querying. In a honeypot environment, it may be reasonable to detect any sequences, but in the SMB natural environment, it's not practical. So, I needed to treat SMB individual events as session objects to improve analysis and detection of their behaviors.

## From SMB Lifecycle Model to Interaction Level Model
To analyze SMB behavior, it's important to define them first. Initially, I considered a lifecycle model to map SMB commands with detection values for each stage.

*Detection values represent the potential impact and investigative priority within a honeypot context rather than an assessment of malicious intent.

| Lifecycle Stage         | Classification Criteria                                  | Detection Value |
| ----------------------- | -------------------------------------------------------- | --------------- |
| SMB Discovery           | NEGOTIATE observed                                       | Low             |
| Authentication Attempts | SESSION_SETUP observed, but no successful authentication | Low             |
| Authenticated           | Successful SESSION_SETUP observed                        | Medium          |
| Share Access            | TREE_CONNECT observed                                    | Medium          |
| File Access             | NT_CREATE observed                                       | Medium          |
| File Activity           | READ, WRITE, DELETE, RENAME, etc. observed               | High            |
| Advanced Operations     | TRANS, NT_TRANSACT, IOCTL, etc. observed                 | High            |
| Logoff                  | LOGOFF observed                                          | Low             |

The detection value increases as the lifecycle stage progresses because SMB commands become more capable. The intention was to record a session at the highest lifecycle stage, so that I could answer questions such as how many sessions reached which impactful stage.

However, as more SMB sessions are examined, I notice that "LOGOFF" was not the end of the session. It's possible that the SMB session would continue executing commands even after LOGOFF with the "...ANDX" command type. In SMB, commands with the "ANDX" prefix are the command type to chain commands together, and there is a "LOGOFF_ANDX" command as well. Also, if "File Activity" and "Advanced Operations" commands are observed in a session, and I want to query a session based on the lifecycle stage, the data will be skewed if I track only one of those activities.

As a result, I stopped thinking about SMB behavior strictly in terms of a lifecycle model and focused more on how the activity interacted with the SMB and resources.

| Interaction Level | Classification Criteria                               | Detection Value |
| ----------------- | ----------------------------------------------------- | --------------- |
| SMB Discovery     | NEGOTIATE Observed                                    | Low             |
| Authentication    | SESSION_SETUP Observed                                | Medium          |
| Share Access      | TREE_CONNECT Observed                                 | Medium          |
| Share Interaction | NT_CREATE, READ, WRITE, DELETE, TRANS, IOCTL Observed | High           |

Then, I would characterize the session with additional information, such as command outcomes, and track detailed session metrics.

#### Outcome Fields
| Field                   | Meaning                                        |
| ----------------------- | ---------------------------------------------- |
| authenticated           | At least one successful SESSION_SETUP observed |
| authentication_attempts | Number of SESSION_SETUP commands               |
| logoff_observed         | LOGOFF command observed                        |
| authentication_status   | Success / Failure / Unknown (optional)         |
|                         |                                                |
#### Session Metrics
| Field           | Example |
| --------------- | ------- |
| read_count      | 15      |
| write_count     | 2       |
| trans_count     | 4       |
| ioctl_count     | 1       |
| delete_count    | 0       |
| nt_create_count | 3       |

This model suits categories and describes the SMB sessions enough to answer several questions, such as:

**Show me all sessions that interacted with shares:**
```
interaction_level = "Share Interaction"
```

**Show me potential brute force sessions:**
```
interaction_level = "Authentication"
AND authenticated = false
AND authentication_attempts >= 5
```

**Show me WRITE-heavy sessions:**
```
interaction_level = "Share Interaction"
AND write_count > 10
```

Now, these objects need to be created via post-processing, ideally in a new index after events are ingested. Kibana Transform can be used for this purpose, but this phase is out of scope to implement Kibana Transform; instead, I created a Kibana table view to visualize sessions and metrics, and I also created a threshold-based rule based on the interaction level, particularly for "Share Interaction".

What I have in this phase:
- Kibana table
- Runtime aggregation
- Exploratory analysis

Future Approach:
- Transform
- Session index
- Pre-computed metrics

**Benefits**
- Simpler queries
- Threshold rules on sessions
- Better scalability

## Constructing Session-Level Visibility
![[2026-06-14_10-43-14-smb-session-table.png]](screenshots/2026-06-14_10-43-14-smb-session-table.png)

This is the Kibana table for session visualization. Each row represents an SMB session, with "flow_id" as the key and "src_ip"; each column is a metric characterizing the session. This table transforms individual SMB events into session-oriented views, enabling analysts to identify behavioral patterns that are difficult to observe from raw event streams alone.

**Global Filter:**
```
event_type.keyword : "smb"
```

| Field                       | KQL                                                                                                                                          |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| \<command\>_count           | ```count(smb.command.keyword, kql='smb.command.keyword : *<command>*')```                                                                    |
| duration_seconds            | ```(max(@timestamp) - min(@timestamp))/1000```                                                                                               |
| session_setup_success_count | ```count(smb.command.keyword, kql='smb.command.keyword : *SESSION* and (smb.status : "STATUS_SUCCESS" or smb.status: "UNKNOWN_00_0000")')``` |
| event_count                 | ```count(flow_id)```                                                                                                                         |

## Detection Rule Creation
While session-level indices would provide a more robust foundation for these detections, meaningful behavioral coverage can still be achieved through event-level thresholding informed by session analysis. Share Interaction, in terms of event/command counts (not session count), is less than 1% of the total SMB counts for May 2026, so it's a much lower number than other interactions, so it's likely that such an interaction may not be detected with session rate and volume-based detection.

## SMB Share Interact Command - 30-min Volume Threshold Exceeded

### Rule Creation Model

#### 1. Define Behavior
- Elevated SMB share interaction commands (READ/WRITE/TRANS/DELETE/IOCTL) activity

#### 2. Define Detection Metric
- SMB share interaction commands count per 30-min

#### 3. Define Baseline and Behavioral Segmentation
- 30-day historical window May 2026
- derive normal range or percentile (P95/P99)

![[2026-06-12_21-03-40-smb-share-interaction-30m-may1-may14.png]](./screenshots/2026-06-12_21-03-40-smb-share-interaction-30m-may1-may14.png)

![[2026-06-12_21-04-26-smb-share-interaction-30m-may14-may31.png]](./screenshots/2026-06-12_21-04-26-smb-share-interaction-30m-may14-may31.png)

[Sample Data](https://docs.google.com/spreadsheets/d/1qJIwov05d8ZkK3UoDEqQLLoEzCQuFXjK8x9rIDAHxNc/edit?gid=1536139125#gid=1536139125)

#### Behavioral-Based Segmentation:

![[2026-06-14_11-20-07-smb-share-interaction-plot.png]](./screenshots/2026-06-14_11-20-07-smb-share-interaction-plot.png)
Looking at the plot of the Share Interaction session count, I observed a relatively gradual increase up to approximately 15 interactions. Considering the honeypot environment, I treated the relatively stable lower portion of the distribution as representative of routine activity and selected 15 as the baseline threshold. Then, a steep slope can be observed around the 20 mark, and based on the distribution of counts or the slope of the graph, I segmented them as "High".
- Baseline: < 15
- Medium Activity Level: 15-19
- High Activity Level: ≥ 20

#### 4. Define Rule Execution
- Evaluation interval: 30min
- Look-back: small buffer (e.g., 3 min)

#### 5. Create Rule
- Medium Alert
    - Threshold Based - Medium Activity Level: 15-19
        - **Hypothesis**: Elevated SMB Share Interaction activity exceeding routine behavioral levels may indicate increased automated enumeration or targeted attempts to interact with SMB resources.
    - Threshold Based - High Activity Level: ≥ 20
        - **Hypothesis**: Sustained or concentrated surges in SMB Share Interaction activity may indicate aggressive automated interaction, exploitation attempts, or abnormal resource access behavior warranting further investigation.
#### 6. Validate and Tune

###### Medium Activity Level
Observed a total of 42 alerts for May 2026. The screenshot below shows samples of alerts. It shows that triggered alerts correspond to the activity exceeding the threshold, which is expected behavior. However, considering the total number of 42 alerts, it may be too noisy. Given the alert timing, multiple alerts are triggered each day. So, I would set the rule to suppress the alert, triggering it once per day. It would reduce the total number of alerts while maintaining awareness of the behavior.

![[2026-06-12_21-00-52-smb-share-interaction-30m-med-alerted-may1-may14.png]](./screenshots/2026-06-12_21-00-52-smb-share-interaction-30m-med-alerted-may1-may14.png)

###### High Activity Level
Observed 11 alerts. I would not change the threshold or set suppression for this.
![[2026-06-12_21-14-50-smb-share-interaction-30m-may1-may14-high-alerted.png]](./screenshots/2026-06-12_21-14-50-smb-share-interaction-30m-may1-may14-high-alerted.png)

##### Dionaea Honeypot Behavior Observation
While working on the initial detection rule, I observed some SMB status codes with "UNKNOWN_" prefix. It's not a formal status message from the SMB specification. So I investigated the Suricata implementation and found that its format is used when Suricata cannot determine the type of DOS ERROR mode status.

DOS ERROR mode is a legacy SMB feature that returns the command status in a different format. Also, looking at the implementation of Dionaea, I was not able to find that Dionaea takes the DOS ERROR mode into account when constructing a response. Therefore, Suricata represented these responses using generic `UNKNOWN_*` status values because the DOS ERROR format could not be mapped to the standard SMB status naming convention. These values still represent valid SMB session outcomes, and the ratio of status with "UNKNOWN_" was much smaller, but it can be significant to miss it for this behavioral analysis.

## Detection Rule Recommendation
Based on these rules, for threshold-based rules, my recommendation as follows:

1. Activity Coverage: **Volume**
2. Protocol Behavior: **SMB Share Interaction Commands**
3. Behavioral Segmentation: **Based on activity behavior**
4. Environmental Activity Factor: **Honeypot**

| Severity | Threshold (30 min) | May 2026 Observations                 | Recommendation                |
| -------- | ------------------ | ------------------------------------- | ----------------------------- |
| Baseline | `< 15`             | Routine activity                      | No alert                      |
| Medium   | `15–19`            | 42 alerts/month (~3–4 on active days) | Daily suppression recommended |
| High     | `≥ 20`             | 10 alerts/month                       | No suppression                |

### Activity Coverage
Volume-based activity is ideal for this case, as it focuses on the existence and trend of those activities rather than the rate. Also, the rules defined above would complement those in Phase 5A.

### Protocol Behavior
There are other interaction levels to consider, but the Share Interaction level would be the most valuable for adding a detection layer to existing rules focused on SMB behavior, providing higher-fidelity alerts.  

### Behavioral Segmentation
Baseline activity was determined by examining activity levels in 30-minute intervals and heuristically defining routine honeypot activity as the baseline.

### Environmental Context
In a honeypot environment, it's common to observe routine scanning or enumeration activities.

### Recommended Use Cases
- Honeypot environments monitoring opportunistic SMB interactions.
- Supplemental behavioral coverage alongside volumetric SMB detections.
- Detection of low-frequency resource access behaviors that may not trigger rate-based rules.

### Operational Considerations
- Medium alerts benefit from daily suppression.
- High alerts were sufficiently infrequent to remain unsuppressed.
- Thresholds should be recalibrated for non-honeypot environments.

## Geographic and Infrastructure Observations
I extended the session table to group session details by source ID, country, and AS, and to show aggregated event counts for higher-level views of SMB sessions.

### Dominant IP Analysis
By focusing on the dominant IP from May 7, 2026, to May 8, 2026, the dominant IP alone would not trigger the rules, but shared interaction activities can still be observed.

Looking at the session table by source IP address, the dominant IP is the top row. I can see that most of the sessions stop at the Authentication level, but please note that they executed TRANS commands rather than file-interaction commands. Interestingly, that's the same trend across the top source IP addresses.  

![[2026-06-13_20-22-30-smb-share-interaction-dominant-ip.png]](./screenshots/2026-06-13_20-22-30-smb-share-interaction-dominant-ip.png)
![[2026-06-15_09-10-18-smb-share-interaction-gropu-by-ip-and-as.png]](./screenshots/2026-06-15_09-10-18-smb-share-interaction-gropu-by-ip-and-as.png)

### Broader Source Population Analysis
This is the per AS and country table; the top row is the AS of the dominant IP. Followed by other AS that are internet service providers.

![[2026-06-14_13-42-44-smb-interaction-session-table-as.png]](./screenshots/2026-06-14_13-42-44-smb-interaction-session-table-as.png)

These findings reinforce the hypothesis from Phase 5A that the dominant IP was associated primarily with scanning or reconnaissance activity. However, repeated observations of TRANS commands suggest that additional investigation is warranted to determine whether these behaviors align with known exploitation techniques.

## Lessons Learned
Throughout this phase, there are notable lessons I learned
- Session-level visibility improved the interpretability of SMB behaviors beyond what was possible through individual event analysis.
- Honeypot implementations may only partially emulate protocols and services, introducing telemetry artifacts that should be considered during detection development.
- Detection engineering requires iterative tuning.
- Thresholds reflect environmental baselines rather than universal values.
- Alert tuning should consider suppression strategies in addition to threshold adjustments to balance visibility with operational noise.

## Limitations
During further investigation of the TRANS commands associated with the dominant IP, particularly SMB1_COMMAND_NT_TRANS as represented in Suricata telemetry, I found that the available honeypot telemetry did not provide sufficient visibility into the underlying command payload. Although Dionaea recorded the interaction and Suricata classified the command type, I was unable to determine the specific NT transaction subfunction being requested.

As a result, the nature and intent of these NT_TRANS operations could not be conclusively assessed within the scope of this investigation. 

Additional analysis using alternative telemetry sources, enhanced SMB emulation, or limited packet capture (PCAP) collection would be necessary to obtain deeper visibility into these command payloads and better understand whether they align with known SMB exploitation techniques or benign protocol behaviors.

## Future Work
- Percentile-Based Thresholding
    - Upgrade from heuristic threshold
- Kibana Transform
    - Session indices
    - Pre-computed metrics
- Deeper SMB  Investigation
    - Analysis of share names and resource targets.
    - Correlation of session characteristics across broader geographic populations.
    - Comparison of observed command patterns against documented SMB exploitation techniques.
    - Investigation of TRANS command usage within high-interaction sessions.
    - Limited PCAP collection targeting SMB sessions exhibiting NT_TRANS activity to support payload-level analysis.

## Current Assessment
Based on the findings from Phase 5A and 5B, the activities from the dominant IP still suggest automated SMB reconnaissance behavior focused on network service and share discovery with higher volume and rate. However, observations of SMB Share Interaction behaviors suggest that a subset of SMB sessions progressed beyond reconnaissance into authenticated or resource-access stages. 

While these findings do not independently confirm exploitation, they warrant additional investigation to better understand the nature and intent of the observed activity.

## Confidence Level: Moderate to High
Confidence is based on the consistency between threshold-based detections, session-level observations, and behavioral patterns identified across multiple phases of the investigation. 

However, limitations in honeypot protocol emulation and the absence of payload-level validation prevent assigning greater confidence.

## Summary
I expanded Phase 5 B's SMB detection coverage beyond volumetric observations by introducing session-oriented behavioral analysis. Through the development of interaction-level models, session visualization techniques, and threshold-based detections targeting Share Interaction activities, this phase demonstrated how richer protocol context can improve the interpretability of behavioral detections. 

The investigation also highlighted the importance of telemetry validation, iterative tuning, and environment-specific baselines when designing behavioral detections.

## Refernces

### Documentation
- https://github.com/OISF/suricata
- https://github.com/DinoTools/dionaea

[<< Phase 5A](./phase5A.md)
 | **Phase 5B** | [Phase 6 >>](../phase6/README.md)
