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

---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

