# Data Engineering Projects: Apache NiFi & Kafka

A collection of hands-on data engineering pipelines built with Apache NiFi 2.11.0 and Apache Kafka, covering structured data processing, API ingestion, unstructured log analysis, and real-time event streaming.

## Projects

| # | Project | Focus | Key Tools |
|---|---------|-------|-----------|
| 01 | [Sales Data Pipeline](./01-sales-pipeline) | Record-oriented processing, validation, conditional routing | GetFile, ValidateRecord, QueryRecord, PutFile |
| 02 | [Weather API Ingestion](./02-weather-api-pipeline) | Scheduled API polling, JSON extraction, batching | InvokeHTTP, EvaluateJsonPath, MergeContent |
| 03 | [Log Monitoring & Alerting](./03-log-monitoring-pipeline) | Unstructured text processing, real-time counters | SplitText, RouteText, UpdateCounter |
| 04 | [Kafka + NiFi Integration](./04-kafka-nifi-integration) | Event streaming, producer/consumer pattern | Docker, PublishKafka, ConsumeKafka |

## What each project demonstrates

- **Record-oriented data processing** — treating flat files as structured records instead of raw text, using Record Readers/Writers and SQL-style querying with QueryRecord.
- **API-driven ingestion** — polling external REST APIs on a schedule, extracting values with JsonPath, and solving the "small files problem" with content batching.
- **Unstructured text handling** — splitting and classifying raw log lines using pattern matching, with live in-memory counters for real-time monitoring.
- **Event-driven architecture** — decoupling data producers from consumers through a Kafka message broker, so systems can scale and evolve independently.

## Environment

- Apache NiFi 2.11.0 (Windows)
- Apache Kafka (KRaft mode, single broker via Docker)
- Docker Desktop with WSL2 backend

## Notes

Each project folder contains:
- `README.md` — architecture explanation and processor breakdown
- `flow-definition.json` — the exported NiFi flow, importable directly into a running NiFi instance
- `screenshots/` — visual reference of the working flow
- Sample input data where applicable

To import a flow: in NiFi, drag a **Process Group** onto the canvas, choose **Import**, and select the corresponding `flow-definition.json`. Controller Services (Record Readers/Writers, Kafka connection services) will need to be re-enabled manually after import.
