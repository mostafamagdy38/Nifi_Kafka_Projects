# Sales Data Pipeline

A record-oriented ETL pipeline that ingests CSV sales data, validates it, splits it into categories based on order value, and writes the results as JSON.

## Architecture

```
GetFile → ValidateRecord → QueryRecord → RouteOnAttribute (implicit via SQL) → PutFile
                  ↓ invalid
              PutFile (invalid records)
```

## Flow

1. **GetFile** watches an input directory for new CSV files.
2. **ValidateRecord** reads the CSV using a `CSVReader` controller service and writes it out as JSON via `JsonRecordSetWriter`. Records that fail schema validation are routed separately.
3. **QueryRecord** runs SQL directly against the in-flight FlowFile content — `SELECT * FROM FLOWFILE WHERE amount > 1000` — splitting records into `big_orders` and `normal_orders` relationships without needing an external database.
4. **PutFile** (x3) writes each category to its own output directory as JSON.
5. **UpdateAttribute** generates a unique filename (`${filename}_${now():format('yyyyMMddHHmmssSSS')}_${UUID()}`) before each write, avoiding file name collisions in the output directories.

## Key concepts demonstrated

- **Record-oriented processing**: treating CSV rows as structured records rather than raw text, enabling schema-aware transformations.
- **In-flight SQL querying**: using `QueryRecord` to filter and route data without a database round-trip.
- **Controller Services**: sharing `CSVReader` / `JsonRecordSetWriter` configurations across multiple processors.
- **Data quality gaps**: records that don't match any downstream condition (e.g. negative amounts) are silently dropped unless explicitly handled — a real-world lesson in why strict schema validation matters.

## Sample data

`sample-data/sales_sample.csv` — a small CSV with intentionally invalid rows (missing country, negative amount) to exercise the validation and routing logic.

## Prerequisites

- Apache NiFi 2.x
- Controller Services: `CSVReader`, `JsonRecordSetWriter` (must be created and enabled after import)
