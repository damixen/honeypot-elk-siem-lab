# Container Health - Honeypots
## Purpose
This is to observe resource usage on the container, focusing on honeypots. Please note that there is "CPU Activity" and "CPU Usage". "CPU Activity" tracks changes in the CPU tick rate to indicate CPU activity. "CPU Usage" tracks CPU usage metrics returned by Metricbeat. The reason is that "CPU Activity" complements the case where the container CPU stats are empty occasionally.   

## Questions Answered
- Which honeypot container used more resources?

![[2026-06-16_09-46-47-container-honeypot.png]](../screenshots/2026-06-16_09-46-47-container-honeypot.png)

![[2026-06-16_09-47-06-container-honeypot.png]](../screenshots/2026-06-16_09-47-06-container-honeypot.png)