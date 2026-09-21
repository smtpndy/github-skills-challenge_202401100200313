# GitHub Challenge

## AIOps scenario

The project monitors a synthetic `payment-service`. It uses response time, CPU, memory, and logs to find service problems early. The main problem is slow payment requests combined with high resource usage and timeout errors.

The main components are simple:

- `AnomalyDetector` checks the metrics and log level.
- `EventProducer` publishes anomaly events.
- `EventTopic` stores events in memory.
- `EventConsumer` reads the events.

## Operational data

The data is in `data/service_data.json` and contains 10 records from `10:00` to `10:09`.

- Metrics: `response_time_ms`, `cpu_percent`, and `memory_percent`.
- Log fields: `log_level` and `message`.
- `timestamp` shows when each observation happened.

From `10:00` to `10:04` and `10:07` to `10:09`, the service looks normal. Response time is about `120-150 ms`, CPU is `42-57%`, memory is `51-57%`, and the logs are successful `INFO` messages.

The unusual records are at `10:05` and `10:06`. Response time increases to `610` and `640 ms`, CPU reaches `75%` and `94%`, and memory reaches `70%` and `91%`. Both records contain timeout errors.

## Task 3: Anomaly detection

The detector processed all 10 records and found 2 anomalies:

- `10:05`: high response time and an `ERROR` log.
- `10:06`: high response time, high CPU, high memory, and an `ERROR` log.

No normal records were flagged. The original detector only checked `WARNING` logs, so the `ERROR` check was identified as an improvement.

One limitation is that the detector uses fixed thresholds. It does not learn normal patterns or detect changes over time. A better version could use trend-based thresholds and more log severities.

## Task 4: Event flow

The flow is:

`data -> AnomalyDetector -> EventProducer -> EventTopic -> EventConsumer`

The original run detected the anomalies, but the consumer received 0 events because the producer and consumer were connected to different topics.

## Task 5: Investigation and correction

- Issue 1: producer and consumer used different topics.
- Issue 2: `ERROR` logs were not included in the detector reason.
- The corrections were to use one shared topic and check both `WARNING` and `ERROR` logs.

## Task 6: Final execution

After the corrections, the pipeline processed 10 records, detected 2 anomalies, and consumed 2 events. The tests passed successfully.

## Reproduce the work

From the project root, run:

```bash
PYTHONPATH=src python -m pytest -q
python src/aiops_pipeline.py
```

The expected final output is 10 records processed, 2 anomalies detected, and 2 events consumed.

---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

