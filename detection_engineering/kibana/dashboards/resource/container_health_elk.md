
# Container Health - ELK and Tools

## Purpose
This is to observe resource usage on the container, focusing on ELK and the tool stack. Please note that there is "CPU Activity" and "CPU Usage". "CPU Activity" tracks changes in the CPU tick rate to indicate CPU activity. "CPU Usage" tracks CPU usage metrics returned by Metricbeat. The reason is that "CPU Activity" complements the case where the container's CPU stats are occasionally empty.   

## Questions Answered
- Which ELK stack or Tool container used more resources?

![[2026-06-16_09-45-44-container-elk.png]](../screenshots/2026-06-16_09-45-44-container-elk.png)

![[2026-06-16_09-46-08-container-elk-2.png]](../screenshots/2026-06-16_09-46-08-container-elk-2.png)