# Threshold Rule Creation Model

This is a model I used to define threshold-based rules in my case studies. What I found useful about this model is that it helps me not only define the purpose of the rule but also scope the data set and its granularity, which I found very important for visualization and threshold determination.  

## Model
#### 1. Define Behavior
- e.g., Excessive SMB command activity (daily volume)

#### 2. Define Detection Measurement
- Determine what to measure.
- Define aggregation granularity.
- e.g., SMB command count per hour

#### 3. Define Baseline and Behavioral Segmentation
- X-day historical window
- derive normal range or percentile (P95/P99)
#### Behavioral-Based Segmentation:
Define activity groupings using observed distributions,
percentiles, or operational requirements.
Example:
- Baseline: < 200 events
- Low: 200–999 events
- Medium: 1000–1999 events
- High: ≥ 2000 events

#### 4. Define Rule Execution
- evaluation interval: 1 hour
- look-back: small buffer (e.g., 3 min)

#### 5. Create Rule
- Threshold-based alert on aggregated metric

#### 6. Validate and Tune
- Evaluate against historical data.
- Compare against known spikes or incidents.
- Assess alert volume and analyst burden.
- Adjust thresholds, windows, or suppression as needed.

## Examples
- [[SMB Negotiate Command - 12h Volume Threshold Exceeded]](../../case-study/smb-traffic-spike-honeypot/phase5/phase5A.md#rule-1-smb-negotiate-command---12h-volume-threshold-exceeded)
- [SMB Negotiate Command - 5-Minute Activity Rate Threshold Exceeded](../../case-study/smb-traffic-spike-honeypot/phase5/phase5A.md#rule-2--smb-negotiate-command---5-minute-activity-rate-threshold-exceeded)





