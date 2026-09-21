# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!


---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

--------------------------------------------------------------------------------------------------
## Task 1: Set Up and Understand the Environment

### AIOps Scenario

This project monitors a simulated `payment-service`. The service produces
operational data containing response times, CPU usage, memory usage, log levels,
and log messages.

The operational problem is to identify unusual behavior in the payment service,
such as slow requests, high resource usage, service timeouts, and concerning
log events. Detecting these issues helps the operations team investigate them
before they affect users.

AIOps is used in this assessment to analyze service telemetry and logs
automatically, identify anomalous behavior, create events for detected issues,
and pass those events through a simple processing workflow.

### Repository Components

- **Operational data:** `data/service_data.json` contains the synthetic
	payment-service records.
- **Metrics and logs:** Each record contains metrics such as
	`response_time_ms`, `cpu_percent`, and `memory_percent`, along with log
	information such as `log_level` and `message`.
- **Anomaly detection:** `src/anomaly_detector.py` checks the operational
	records against defined thresholds and identifies anomalies with reasons.
- **Event production:** `src/event_producer.py` publishes detected anomaly
	events to a topic.
- **Event topics:** `src/event_topic.py` provides an in-memory topic that
	stores published events.
- **Event consumption:** `src/event_consumer.py` reads events from a topic.
- **Final AIOps processing:** `src/aiops_pipeline.py` loads the data, runs
	anomaly detection, publishes detected events, consumes events, and reports
	the final processing results.

The provided application structure and components remain unchanged. This
section documents their purpose before the workflow is executed.

---------------------------------------------------------------------------------------------------------------------
## Task 2: Analyse Logs and Metrics

The operational data is stored in `data/service_data.json` and contains 10
records for the `payment-service`.

The metric fields are:

- `response_time_ms`: request response time in milliseconds
- `cpu_percent`: CPU utilization percentage
- `memory_percent`: memory utilization percentage

The log fields are:

- `log_level`: severity of the log message
- `message`: description of the service activity

The `timestamp` field records when each observation occurred. The timestamps
are in ISO 8601 format and increase at one-minute intervals from
`2026-09-20T10:00:00` to `2026-09-20T10:09:00`.

Records from 10:00 through 10:04 and from 10:07 through 10:09 appear normal.
They have response times below 500 ms, CPU and memory usage below 80%, and
informational log messages.

The record at 10:05 appears anomalous because the response time is 610 ms and
the log level is `ERROR`, with the message `Payment service timeout`.

The record at 10:06 is also anomalous. It has a response time of 640 ms, CPU
usage of 94%, memory usage of 91%, and an `ERROR` log level with the message
`Database connection timeout`.

These observations are based on the thresholds used by the provided anomaly
detector.
