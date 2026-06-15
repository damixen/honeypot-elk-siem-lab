# Conclusion

## Investigation Conclusions
After completing this investigation, I concluded that the dominant SMB activity aligns most closely with automated reconnaissance and service discovery behavior. The observed activity exhibited sustained volume and rate characteristics consistent with automated operation rather than manual interaction. Furthermore, approximately 99% of observed SMB commands did not progress to Share Interaction behaviors, reinforcing the assessment that the dominant activity focused primarily on network and service discovery.

Although limited Share Interaction behaviors were observed, primarily involving NT_TRANS commands, there was insufficient telemetry to investigate these command payloads in greater detail.

Also, considering the rate and volume of the activity, it indicates that those activities were most likely performed in an automated setup rather than manually.

I did not observe evidence suggesting coordinated behaviors between the dominant IP and other source IPs originating from the same autonomous system or country. One limitation of this investigation is that this honeypot was deployed on a U.S. location and is the only honeypot I manage, so I do not have visibility into the internet-wide activity of this dominant IP.

## Confidence Level: Moderate to High

Confidence in these conclusions is supported by consistent observations across multiple analytical approaches, including volumetric analysis, session-level behavioral analysis, and validation through detection development activities. However, limitations in protocol emulation, incomplete visibility into certain SMB command payloads (such as NT_TRANS), and the absence of broader internet-wide telemetry prevent assigning a higher level of confidence.

## Detection Conclusions
Volumetric detections provided broad coverage.
Session-oriented detections improved interpretability.
Behavioral thresholds required an environmental context.

This dominant activity showed that multiple layers of detection provide broad coverage and complement each other. The activity was detected by the volumetric detection rule for long-term behavior (12h), but not by the short-term (5min) behavior. This observation highlighted the value of layered detection, in which different detection strategies provide coverage across varying temporal scales and behavioral characteristics.

Also, working on session-oriented detections gave me the opportunity to define the session context, such as the interaction level, and helped with my overall understanding of the SMB system. The session-oriented data or indices may not be avaiable when performing post-processing, but I was able to create an SMB session table that provides much better visibility without advanced data operations, which was sufficient for this case study.

## Telemetry Conclusions
- Honeypot telemetry can reveal meaningful attacker behaviors. 
- Protocol emulation limitations influence analytical confidence.
- Additional visibility (e.g., targeted PCAP) would improve investigations.

Honeypot telemetry can reveal meaningful activities and their trends very well. However, several observations were made during this case study, indicating that the honeypot may have limited protocol emulation, which may limit the ability to collect activity data and can influence analytical confidence or lead to misinterpretation of behavior. Therefore, understanding and validating honeypot behavior becomes increasingly important as investigations move beyond high-level observations into deeper protocol analysis.

## What's Next
As I mentioned in [Phase 5B](../phase5/phase5B.md#future-work), numerous opportunities remain for both deeper behavioral investigations and engineering improvements in telemetry collection and analysis workflows. Rather than representing a definitive conclusion, this case study opened additional pathways for future investigation and the development of detection methods.


[<< Phase 5](../phase5/README.md)
 | **Phase 6**
