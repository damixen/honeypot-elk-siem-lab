# Detection Rule Engineering Cheat Sheet

This cheat sheet provides a quick, repeatable workflow for designing detection rules from security telemetry. It combines structured rule design with a mental modeling pipeline to ensure consistency, reusability, and operational relevance.

---

# 1. Rule Engineering Loop (Core Thinking Model)

Use this loop for every detection:

```
1. What behavior am I observing?
2. How do I measure it in logs?
3. What is normal (baseline)?
4. What is abnormal (detection condition)?
5. How will this behave operationally?
6. Does it actually work in real data?
```

---

# 2. Rule Creation Model (Structured Workflow)

Follow these steps to formalize a rule:

## 1. Define Behavior

- What is happening?
- Example: SMB negotiation spike, SSH login failures, HTTP bursts

---

## 2. Define Detection Metric

- What is the measurable unit?
- Examples:

  - events per IP
  - requests per minute
  - command count per session

---

## 3. Define Baseline & Segmentation

- Establish normal behavior from historical data
- Methods:

  - fixed thresholds
  - percentiles (P95 / P99)
  - behavioral grouping (Low / Medium / High)

---

## 4. Define Rule Execution

- Time window (e.g., 5m, 1h, 12h)
- Evaluation interval (e.g., every 5 min)
- Lookback buffer (avoid boundary loss)

---

## 5. Create Rule

- Implement detection logic:

  - threshold-based
  - sequence-based
  - correlation-based

---

## 6. Validate & Tune

- Test against known traffic
- Evaluate alert volume
- Adjust thresholds and windows
- Apply suppression if needed

---

# 3. Fast Rule Construction Checklist

Before finalizing any rule, verify:

## Behavior

- [ ] Is the behavior clearly defined?

## Measurement

- [ ] Can it be directly measured in logs?

## Baseline

- [ ] Do I understand what "normal" looks like?

## Detection

- [ ] Is the trigger condition explicit?

## Operations

- [ ] Is alert noise considered?
- [ ] Is suppression needed?

---

# 4. Detection Pattern Library (Reusable Models)

Most rules fall into one of these patterns:

## 1. Spike Detection

- Sudden increase in activity volume
- Example: SMB negotiation spikes

## 2. Rate-Based Detection

- Events per time unit
- Example: SSH login failures per minute

## 3. Sequence Detection

- Ordered protocol behavior
- Example: NEGOTIATE → SESSION_SETUP → TREE_CONNECT

## 4. Correlation Detection

- Relationship between multiple entities
- Example: IP + ASN + country clustering

## 5. Persistence Detection

- Repeated behavior over time
- Example: recurring scanning activity

---

# 5. Baseline Strategies

Choose one:

## Static Threshold

- Fixed number (e.g., >1000 events)

## Statistical Baseline

- Mean / standard deviation

## Percentile-Based

- P95 / P99 from historical data

## Behavioral Segmentation

- Low / Medium / High tiers

---

# 6. Operational Considerations

Every rule must answer:

- Expected alert volume?
- Noise level acceptable?
- Suppression required?
- Analyst response action?
- Severity level?

---

# 7. Mental Shortcut (Ultra Fast Version)

When building a rule, think:

```
Behavior → Metric → Baseline → Condition → Impact
```

If any step is missing, the rule is incomplete.

---

# 8. Rule Quality Test

A good detection rule should:

- Detect meaningful behavior, not noise
- Be explainable in one sentence
- Have a clear baseline reference
- Include tuning strategy
- Be reusable across similar protocols

---

# 9. Example Mental Translation

```
Observed: SMB negotiation spikes
↓
Metric: events per IP per 12h
↓
Baseline: P95 of 30-day data
↓
Condition: count > threshold
↓
Operational: suppress per IP per 24h
```

---

# 10. Core Principle

> A detection rule is not a query.
> It is a structured representation of behavioral abnormality in time-series telemetry.
