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

## Task 3: Identify Anomalies

We used the provided anomaly-detection mechanism from the project to process the operational data and review the detected results.

### 1. Detection process and verification
The detection pipeline reads all records from data/service_data.json, runs each record through AnomalyDetector.detect(), and keeps only the records that are flagged as anomalies. From the actual run using the project pipeline, the system processed 10 records and detected 2 anomalies.

### 2. Anomalies detected
The detected anomalies are:
- 2026-09-20T10:05:00
  - service: payment-service
  - response_time_ms: 610
  - cpu_percent: 75
  - memory_percent: 70
  - log_level: ERROR
  - message: Payment service timeout
  - reasons: High response time

- 2026-09-20T10:06:00
  - service: payment-service
  - response_time_ms: 640
  - cpu_percent: 94
  - memory_percent: 91
  - log_level: ERROR
  - message: Database connection timeout
  - reasons: High response time, High CPU utilization, High memory utilization

These two records clearly stand out from the surrounding data and match the expected abnormal behaviour in the dataset.

### 3. Relevant metric and log information
The important signals are:
- response_time_ms increases from the normal 120-150 ms range to 610-640 ms
- cpu_percent rises from about 42-57% to 75-94%
- memory_percent rises from about 51-57% to 70-91%
- log_level changes from INFO to ERROR
- message fields show Payment service timeout and Database connection timeout

This combination gives enough evidence to understand why the records were flagged.

### 4. Normal vs anomalous observations
Normal observations:
- 2026-09-20T10:00:00 to 2026-09-20T10:04:00
- 2026-09-20T10:07:00 to 2026-09-20T10:09:00

These records are healthy because they have low latency, moderate CPU and memory usage, and INFO-level success messages.

Anomalous observations:
- 2026-09-20T10:05:00
- 2026-09-20T10:06:00

These records are abnormal because they have very high response time, a sharp CPU spike, memory pressure, and timeout errors.

### 5. Expected anomaly missed or false positive check
From the project result, no normal event was incorrectly flagged. The detector did not flag the healthy records, which is good.

However, one important issue is that the method did not include the log-based reason properly. In the dataset, the abnormal records have ERROR log levels, but the detection logic only checks for WARNING in the code. So the relevant log condition is present in the data, but the reason "Error log detected" is not added to the anomalous event output. This means an expected log-related warning signal was effectively missed by the provided detection logic.

### 6. Readability of the result
The detection output is relatively readable because each flagged anomaly includes:
- timestamp
- service name
- anomaly type
- reasons list
- original source record

This makes it easy to see why a record was flagged and what data caused the alert.

### 7. Limitation / possible improvement
One limitation of this detection approach is that it uses fixed thresholds and does not consider temporal trends or severity patterns in a more advanced way. For example, an ERROR log event should probably be treated as a relevant anomaly signal even if the metric values are not yet above the threshold. A possible improvement would be to include log severity checks for ERROR and WARN, and to combine metric thresholds with trend-based detection so the system can catch a wider range of real incidents more reliably.

## Task 4: Verify the AIOps Event Flow

For this task, we used the existing event-stream simulation in the project to check whether an anomaly can move through the complete workflow.

### Role of the components
- Producer: takes the detected anomaly and sends it into the event system.
- Topic: acts as the in-memory channel where messages are stored and later read by the consumer.
- Consumer: reads events from the topic and receives the message.
- Event/message: the actual anomaly record that carries information such as timestamp, service name, type, and reasons.

### Execution result
I ran the provided workflow using the project’s own pipeline and the result was:
- records_processed = 10
- anomalies_detected = 2
- events_consumed = 0

The detected anomalies were the two timeout-related records at 10:05 and 10:06. These were correctly identified by the detector and were available as anomaly events in the pipeline.

### Verification of the flow
1. An anomaly identified by the detection process results in an event.
   - Yes, the detector produced 2 anomaly events.

2. The event is passed to the producer.
   - Yes, the event is passed to EventProducer.publish() when the detector finds a match.

3. The producer publishes the event to the appropriate topic.
   - In the code, the producer is connected to a topic named service-events, but the consumer is connected to a different topic named anomaly-events.

4. The consumer receives the event from the topic.
   - No, the consumer did not receive any event in the actual execution because it was subscribed to a different topic.

5. The consumer processes the received event.
   - This step is not reached in the current flow because no event is present in the consumer topic.

6. The processed event reaches the downstream AIOps component.
   - Not in the current execution, as the event flow is broken between the producer and consumer.

### Conclusion
The detection part works correctly, but the event flow is not fully complete in the current implementation. The anomaly is detected and created as an event, but the producer and consumer are connected to separate topics, so the event never reaches the consumer. This means the complete downstream event-processing chain is not working as intended in the current version of the project.

This is a good example of how a system can detect the problem but still fail to propagate it through the pipeline if the event routing is incorrect.

## Task 5: Investigate and Correct the Workflow

- The producer and consumer were connected to different topics.
- The detector checked WARNING logs but ignored ERROR logs.
- These issues explain why anomalies were detected but not consumed correctly.
- The corrections identified and tested were to use one shared topic and include ERROR logs in the detection check.
- The code was then restored to the original version to demonstrate the initial workflow behaviour.

## Task 6: Execute the End-to-End Pipeline

- The original pipeline processed 10 records.
- It detected 2 anomalies at 10:05 and 10:06.
- It consumed 0 events because of the topic mismatch.
- The existing tests passed: 9 tests passed successfully.

## Reproducing the Demonstration

1. Open a terminal in the project root.
2. Run the tests with `PYTHONPATH=src python -m pytest -q`.
3. Run the original pipeline with `python src/aiops_pipeline.py`.
4. Expected result: 10 records processed, 2 anomalies detected, and 0 events consumed.
5. The 0 consumed events demonstrates the topic mismatch described in Task 5.

---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

