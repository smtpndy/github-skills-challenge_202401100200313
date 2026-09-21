# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!

## Assessment Summary

### AIOps scenario
This assessment simulates an AIOps workflow for a production service called payment-service, which processes customer payment requests. The service emits operational telemetry including response time, CPU utilization, memory utilization, and log severity. The goal is to detect when the service begins to behave abnormally and surface the problem as an event that can be investigated or acted on.

### Operational problem being addressed
The payment-service occasionally experiences timeout errors and sharp rises in resource consumption. During these periods, response time increases dramatically, system pressure climbs, and some requests fail. These signs often indicate a degrading service condition that could lead to customer-facing impact if not detected early.

### Purpose of AIOps in this assessment
AIOps is used here to turn raw service telemetry into actionable insight. Instead of waiting for a full outage, the workflow analyzes patterns across metrics and logs to identify anomalies, emit structured event records, and highlight the likely operational issue for investigation.

### Major components and their purpose
- AnomalyDetector: reviews each telemetry record and flags abnormal behavior such as high response time, elevated CPU, high memory use, or error-related log signals.
- EventTopic: acts as an in-memory event stream, representing the message bus that carries events between producers and consumers.
- EventProducer: publishes detected anomaly events to the event topic so they can be passed downstream.
- EventConsumer: reads the anomaly events from the topic and makes them available for the rest of the workflow.

These components represent a simple AIOps pipeline in which telemetry is analyzed, anomalies are published, and downstream systems can consume and act on the findings before they become larger incidents.

## Operational Data Analysis

Based on the synthetic operational records in data/service_data.json, the following observations are evident:

1. Metrics fields
   - response_time_ms: measures request latency, with values around 120-150 ms during normal operations and severe spikes at 610 and 640 ms during abnormal periods.
   - cpu_percent: measures central processing usage, typically around 42-57% during normal operation and rising to 75-94% during anomalous periods.
   - memory_percent: measures memory utilization, usually around 51-57% during normal activity and increasing to 70-91% during abnormal activity.

2. Log information fields
   - log_level: indicates severity, such as INFO for normal operational events and ERROR for degraded or failing conditions.
   - message: provides the human-readable event description, such as Payment request processed successfully versus timeout-related error messages.

3. How timestamps are used
   - The timestamp field is an ISO-8601 datetime recorded for each service event.
   - The dataset uses one-minute intervals from 2026-09-20T10:00:00 through 2026-09-20T10:09:00.
   - The timestamps show a sequence from normal behavior, through a short period of degradation, and then back to normal service behavior.

4. Normal behaviour
   - Observations at 10:00 through 10:04, 10:07 through 10:09 show stable request processing with response times roughly 120-150 ms.
   - CPU usage remains largely in the mid-40s to low-50s percent, and memory stays around 51-57%.
   - log_level is INFO and the message states Payment request processed successfully.
   - This pattern indicates healthy operation with no significant service instability.

5. Unusual behaviour
   - The records at 10:05 and 10:06 are clearly abnormal.
   - response_time_ms jumps to 610 ms and then 640 ms, far above the normal range.
   - cpu_percent climbs to 75% and then 94%, showing heavy system load.
   - memory_percent rises to 70% and then 91%, indicating memory pressure.
   - log_level changes to ERROR and the messages identify Payment service timeout and Database connection timeout.
   - These observations represent operational degradation and likely service instability or resource exhaustion.

This dataset demonstrates a classic AIOps pattern: a short period of abnormal operational signal emerges from telemetry and log data, and that pattern stands out clearly against the surrounding normal service behavior.

---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

