# Log Monitoring & Alerting Pipeline

A text-processing pipeline that ingests unstructured application log files, classifies each line by severity, tracks error counts in real time, and separates critical alerts from routine logs.

## Architecture

```
GetFile → SplitText → RouteText ─┬─ error_line → UpdateCounter → UpdateAttribute → PutFile (critical_alert)
                                  ├─ warn_line  → MergeContent → UpdateAttribute → PutFile (warning)
                                  └─ info_line  → MergeContent → UpdateAttribute → PutFile (info_logs)
```

## Flow

1. **GetFile** picks up `.log` files from a watched directory.
2. **SplitText** breaks the file into one FlowFile per line, since log files have no fixed schema and must be evaluated line by line.
3. **RouteText** classifies each line by substring match (`ERROR`, `WARN`, `INFO`) into separate relationships.
4. **Error lines** go through **UpdateCounter**, which increments an in-memory NiFi counter (viewable live under *Counters* in the UI — no external code required) before being written immediately, one file per error, for fast alerting.
5. **Warning and info lines** are batched via **MergeContent** before being written, since they don't need immediate individual handling.
6. **UpdateAttribute** generates a unique filename per output to avoid file collisions.

## Key concepts demonstrated

- **Unstructured text processing**: handling data with no fixed schema, as opposed to the CSV/JSON pipelines in the other projects.
- **Pattern-based routing**: classifying content with `RouteText` (substring or regex matching) rather than SQL or Expression Language.
- **Built-in observability**: using NiFi's native `UpdateCounter` / Counters UI for live metrics instead of building custom counting logic.
- **Real-time vs. batch handling**: critical events are processed individually and immediately, while routine events are batched for efficiency — a common pattern in production alerting systems.

## Sample data

`sample-data/app.log` — a small log file with a mix of INFO, WARN, and ERROR lines.

## Prerequisites

- Apache NiFi 2.x
