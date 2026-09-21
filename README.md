# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!

## Assessment Summary

### AIOps scenario
This project is based on a simple AIOps use case for a service called payment-service. The service handles payment requests and generates system data such as response time, CPU usage, memory usage, and log messages. The idea is to monitor this data and detect abnormal behaviour before it turns into a bigger service problem.

### Operational problem being addressed
The main issue here is that the service sometimes slows down badly and starts using much more system resources than usual. In those moments, requests take too long, the system gets overloaded, and errors appear in the logs. This kind of situation can affect users and may lead to downtime if it is not noticed early.

### Purpose of AIOps in this assessment
AIOps is useful here because it helps us turn raw monitoring data into something meaningful. Instead of manually checking every metric and log line, we can analyze the data pattern and identify times when the service is behaving unusually. This makes it easier to detect failures earlier and understand what might be going wrong.

### Major components and their purpose
- AnomalyDetector: checks each record and identifies abnormal values such as high response time, high CPU usage, high memory usage, or error-level logs.
- EventTopic: acts like an in-memory message queue, where events are stored before being consumed by another component.
- EventProducer: sends detected anomaly events into the event topic.
- EventConsumer: reads the events from the topic so they can be processed or examined later.

These components together create a very basic AIOps pipeline: collected telemetry is analyzed, suspicious events are published, and the results are made available for further action.

## Operational Data Analysis

We looked at the synthetic data in data/service_data.json and the pattern is pretty clear.

1. Fields that represent metrics
   - response_time_ms: this is the service response time in milliseconds. It is a performance metric.
   - cpu_percent: this shows CPU usage percentage. It is a system resource metric.
   - memory_percent: this shows memory usage percentage. It is also a resource metric.

2. Fields that represent log information
   - log_level: this tells us the severity of the event, such as INFO or ERROR.
   - message: this gives the actual log text, like Payment request processed successfully or Payment service timeout.

3. How timestamps are used
   - The timestamp field records when each event happened.
   - The records are spaced at one-minute intervals, starting from 2026-09-20T10:00:00 and continuing up to 2026-09-20T10:09:00.
   - By looking at the timestamps in order, we can clearly see how the service behaves over time, from normal conditions to a short period of abnormal activity and then back to normal again.

4. Observations that look normal
   - From 10:00 to 10:04, the service seems to be working normally.
   - response_time_ms stays around 120-150 ms, which is a normal range for this dataset.
   - cpu_percent stays around 42-57%, and memory_percent stays around 51-57%.
   - log_level is INFO and the message says Payment request processed successfully.
   - This pattern shows steady and healthy service behaviour.

5. Observations that look unusual
   - The records at 10:05 and 10:06 stand out clearly.
   - response_time_ms rises to 610 ms and then 640 ms, which is much higher than the usual values.
   - cpu_percent jumps to 75% and then 94%, which indicates heavy load.
   - memory_percent rises to 70% and then 91%, which suggests memory pressure.
   - log_level changes to ERROR, and the messages say Payment service timeout and Database connection timeout.
   - These values are clearly abnormal compared to the rest of the dataset and indicate a service issue or resource bottleneck.

Overall, the dataset shows a normal working period, followed by a short abnormal period with higher latency, CPU/memory spikes, and timeout errors. That is exactly the kind of pattern an AIOps system is meant to detect and flag.

---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

