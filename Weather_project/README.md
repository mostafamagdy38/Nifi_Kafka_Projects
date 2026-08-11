# Weather API Ingestion Pipeline

A scheduled data pipeline that polls a public weather API, extracts structured values from the JSON response, classifies readings, and batches them into files.

## Architecture

```
InvokeHTTP (timer-driven, every 30s) → EvaluateJsonPath → RouteOnAttribute (hot/cold) → UpdateAttribute → MergeContent → PutFile
```

## Flow

1. **InvokeHTTP** calls the [Open-Meteo](https://open-meteo.com) API (no API key required) on a 30-second timer, requesting current temperature and wind speed.
2. **EvaluateJsonPath** extracts `temperature` and `wind_speed` from the JSON response into FlowFile attributes using JsonPath expressions (`$.current.temperature_2m`).
3. **RouteOnAttribute** classifies each reading using NiFi Expression Language: `${temperature:toNumber():gt(30)}` for "hot", `${temperature:toNumber():lt(15)}` for "cold".
4. **UpdateAttribute** stamps each FlowFile with a `processed_at` timestamp.
5. **MergeContent** batches multiple readings together (minimum 5 entries or 2 minutes, whichever comes first) before writing, avoiding the "small files problem" that comes with writing a file every 30 seconds.
6. **PutFile** persists the merged batch to disk.

## Key concepts demonstrated

- **Scheduled (timer-driven) execution**: processors that originate their own work on a fixed interval rather than reacting to upstream data.
- **JsonPath extraction**: pulling specific values out of nested JSON without writing custom parsing code.
- **NiFi Expression Language**: an alternative to SQL-based routing (`QueryRecord`) for attribute-based conditional logic.
- **Content batching**: solving the small-files problem common in streaming ingestion pipelines with `MergeContent`'s bin-packing strategy.

## Prerequisites

- Apache NiFi 2.x
- Internet access (calls a public API — no credentials needed)

## Extending this project

- Swap `PutFile` for `PutDatabaseRecord` to persist readings into a relational or time-series database.
- Parameterize the coordinates via FlowFile attributes to poll multiple cities from a single processor.
