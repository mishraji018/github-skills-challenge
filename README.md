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
---------------------------------------------------------------------------------------------------------------------
## Task 3: Identify Anomalies

The provided anomaly detector processed all 10 operational records.

Two anomalous observations were detected:

- At `2026-09-20T10:05:00`, the payment service had a response time of
  `610 ms`, which exceeded the configured threshold.
- At `2026-09-20T10:06:00`, the payment service had a response time of
  `640 ms`, CPU usage of `94%`, and memory usage of `91%`.

The detector correctly distinguished the normal observations from the records
with abnormal metric values. Each generated anomaly event includes the
timestamp, service name, event type, reasons, and original source record.

One issue was observed: the data contains `ERROR` log levels, but the current
detector checks only for `WARNING`. Therefore, the concerning error log
information is not added as a detection reason. This is a limitation and
possible defect to investigate in Task 5.

---------------------------------------------------------------------------------------------------------------------
## Task 4: Verify the AIOps Event Flow

The provided event components were verified using an anomaly event from the
`payment-service`.

The event flow is:

```text
Anomaly Event -> Producer -> Topic -> Consumer -> AIOps Output
```

- **Event/message:** Represents the detected anomaly, including the service,
  timestamp, type, and reasons.
- **Producer:** Publishes the anomaly event.
- **Topic:** Stores the published event in the in-memory event stream.
- **Consumer:** Reads and processes the event from the topic.
- **AIOps output:** Reports the processed anomaly information.

The isolated event-flow test successfully confirmed that an anomaly event was
created, published by the producer, stored in the `anomaly-events` topic, and
received and processed by the consumer.

The provided end-to-end pipeline was then executed with the following result:

```text
Records processed: 10
Anomalies detected: 2
Events consumed: 0
```

The two anomalies were detected, but the final pipeline consumed no events.
The producer currently uses the `service-events` topic while the consumer uses
the `anomaly-events` topic. Because these topic names do not match, the
consumer cannot receive the events published by the producer. This issue is
recorded for correction in Task 5.
---------------------------------------------------------------------------------------------------------------------
## Task 5: Investigate and Correct the Workflow

Two workflow issues were identified.

First, the anomaly detector checked only for `WARNING` log levels, while the
operational data contained concerning `ERROR` log levels. The detector was
updated to recognize both `WARNING` and `ERROR`.

Second, the producer published events to the `service-events` topic while the
consumer read from the `anomaly-events` topic. The consumer was updated to use
the same topic instance as the producer.

After these corrections, the complete pipeline processed 10 records, detected
2 anomalies, and consumed 2 events successfully.
---------------------------------------------------------------------------------------------------------------------
## Task 6: Execute the End-to-End Pipeline

After correcting the anomaly detector and topic mismatch, the provided AIOps
pipeline was executed successfully.

The complete workflow was:

```text
Operational Data -> Anomaly Detection -> Event -> Producer -> Topic -> Consumer -> AIOps Output
```

Execution result:

```text
Records processed: 10
Anomalies detected: 2
Events consumed: 2
```

The final output represented two detected payment-service issues:

- At `2026-09-20T10:05:00`, the response time was `610 ms` and the log level
	was `ERROR`, indicating a payment service timeout.
- At `2026-09-20T10:06:00`, the response time was `640 ms`, CPU usage was
	`94%`, memory usage was `91%`, and the log level was `ERROR`, indicating a
	database connection timeout.

This confirms that the operational data was processed, anomalous behavior was
detected, events were generated and published, the consumer received them,
and the final AIOps output was produced successfully.