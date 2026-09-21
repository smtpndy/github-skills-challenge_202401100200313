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

The file `data/service_data.json` contains 10 records from 10:00 to 10:09.

- Metrics: `response_time_ms`, `cpu_percent`, and `memory_percent`.
- Log fields: `log_level` and `message`.
- `timestamp` shows when each observation happened.

The records from 10:00 to 10:04 and 10:07 to 10:09 look normal. Response time stays around 120-150 ms, CPU stays around 42-57%, memory stays around 51-57%, and the logs are successful INFO messages.

The unusual records are at 10:05 and 10:06. Response time increases to 610 and 640 ms, CPU reaches 75% and 94%, and memory reaches 70% and 91%. Both records contain timeout errors, showing a possible service or resource problem.

The timestamps help compare the service before, during, and after the problem. The values return to normal after 10:06, so the issue appears to be a short period of high load rather than a continuous failure.

## Task 3: Identify Anomalies

The detector processed all 10 records and identified 2 anomalies:

- 10:05: high response time and an ERROR log.
- 10:06: high response time, high CPU, high memory, and an ERROR log.

The normal records were not flagged because their metrics stayed below the configured thresholds and their logs were INFO level. The main issue found was that the original detector checked WARNING logs but not ERROR logs. Checking both levels makes the log signal useful.

A limitation is that the detector uses fixed thresholds and does not learn trends from previous records. For a larger system, trend-based detection could identify gradual changes before they cross a fixed limit.

## Task 4: Verify the AIOps Event Flow

The event flow is:

`data -> AnomalyDetector -> EventProducer -> EventTopic -> EventConsumer`

The detector creates an event when it finds an anomaly, and the producer publishes it to the in-memory topic. The consumer should then read and process the same event.

In the original run, 10 records were processed and 2 anomalies were detected, but 0 events were consumed. The reason was a topic mismatch: the producer used `service-events` while the consumer used `anomaly-events`. The fix is to connect both components to one shared topic.

Therefore, detection and event creation were working, but the downstream part of the pipeline was not completing. After using the same topic for both components, the events can move from the producer to the consumer as expected.

## Task 5: Investigate and Correct the Workflow

- The producer and consumer were connected to different topics.
- The detector checked WARNING logs but ignored ERROR logs.
- These issues explain why anomalies were detected but not consumed correctly.
- The corrections were to use one shared topic and include ERROR logs in the detection check.
- Both corrections were tested successfully with the project tests and pipeline.

## Task 6: Execute the End-to-End Pipeline

- The corrected pipeline processed 10 records.
- It detected 2 anomalies at 10:05 and 10:06.
- It consumed 2 events successfully.
- The tests passed: 9 tests passed successfully.

## Reproducing the Demonstration

1. Open a terminal in the project root.
2. Run the tests with `PYTHONPATH=src python -m pytest -q`.
3. Run the corrected pipeline with `python src/aiops_pipeline.py`.
4. Expected result: 10 records processed, 2 anomalies detected, and 2 events consumed.
5. The consumed events should contain the timeout anomalies from 10:05 and 10:06.

---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

