# Ride-Booking & Operations Analytics | Power BI

A portfolio project exploring how ride-booking data can be modelled and analysed for operational performance, cancellations, service quality, and revenue.

> **Project status: dataset, documentation, and preview are available; reproducibility is still incomplete.** The source CSV and dashboard GIF are present, but the editable Power BI report file (.pbix or .pbip) is not. The dataset upload is confirmed, but its contents have not been independently profiled in this review. Treat the numeric summaries in the findings register as provisional until they are recalculated from the CSV and checked against the Power BI model.

This is an independent portfolio project. It is not affiliated with, endorsed by, or an official product of Uber.

## Dashboard preview

![Ride-booking dashboard preview](Documentation/Dashboard.gif)

The GIF is a visual preview only. It is not a substitute for the editable Power BI report file.

## Project objectives

The documented analysis framework is designed to investigate:

- **Booking outcomes:** completed, cancelled, no-driver-found, and incomplete bookings.
- **Operational performance:** booking volume by time, vehicle type, and location; vehicle-arrival and customer-arrival time metrics where definitions are available.
- **Service quality:** driver and customer ratings, with missing ratings treated as missing rather than zero.
- **Revenue analysis:** booking value, completed-booking revenue, ride distance, and revenue per kilometre.
- **Customer behaviour:** booking frequency and repeat usage, subject to the limits of the available fields.

These are analysis questions, not claims that every relationship or conclusion has already been verified.

## Tools and methods documented

- **Power BI Desktop** for the report and data model
- **Power Query (M)** for data preparation
- **DAX** for KPI definitions
- **Star-schema modelling** with a booking-level fact table and supporting dimensions
- **Git/GitHub** for project documentation and version control

The repository contains proposed implementation steps and measure definitions. Although the source CSV is included, the PBIX/PBIP is not; therefore this README does not claim that the implementation has been run end-to-end or that all formulas have been tested in a working model.

## Dataset and provenance

The file `Data/raw/ncr_ride_bookings.csv` is now included in this repository. Existing project notes describe the dataset as approximately 150,000 booking records and 21 fields for the NCR region of India during 2024; these figures still need to be checked programmatically against the uploaded file. A commonly referenced matching dataset is [Uber Ride Analytics Dashboard — NCR Ride Bookings on Kaggle](https://www.kaggle.com/datasets/yashdevladdha/uber-ride-analytics-dashboard), but confirm that this is the actual source of the file committed here before attributing it. Public availability does not automatically grant redistribution rights; review the source dataset's terms before sharing it further.

Before publishing validated findings, add the exact source URL, attribution, licence/usage terms, and a documented version of the dataset that you are permitted to redistribute. If the data cannot legally be shared, document how reviewers can obtain it and provide a safe, reproducible alternative where possible. Do not commit personal or confidential data.

## Analysis documentation

| File | Purpose |
|---|---|
| [Key insights and validation status](Documentation/Key_Insights.md) | Separates provisional figures from validated findings and records arithmetic checks |
| [DAX measures](Documentation/DAX_Measures.md) | Proposed KPI formulas and measurement caveats |
| [Business questions](Documentation/Business_Questions.md) | Questions to investigate, not pre-decided conclusions |
| [Data dictionary](Documentation/Data_Dictionary.md) | Field definitions and data-model reference; verify against the source schema |
| [Power BI implementation guide](PowerBI/Implementation_Guide.md) | Steps for building the data model and report |
| [Power Query transformation guide](PowerQuery/Transformation_Guide.md) | Proposed M transformations and checks |
| [Dataset notes](Data/raw/README.md) | Current source-data availability and provenance limitations |
| [Validation checklist](Documentation/Validation_Checklist.md) | Remaining work required for a reproducible portfolio release |

## Current repository contents

```text
.
├── README.md
├── .gitignore
├── Data/
│   └── raw/
│       ├── README.md
│       └── ncr_ride_bookings.csv
├── Documentation/
│   ├── Business_Questions.md
│   ├── DAX_Measures.md
│   ├── Dashboard.gif
│   ├── Data_Dictionary.md
│   ├── Key_Insights.md
│   └── Validation_Checklist.md
├── PowerBI/
│   └── Implementation_Guide.md
└── PowerQuery/
    └── Transformation_Guide.md
```

The editable Power BI report, a separate data-quality report, and a separate model-architecture file are not currently present in the repository. The raw source CSV is present at `Data/raw/ncr_ride_bookings.csv`. The guides contain model-design information, but those missing files should not be assumed to exist.

## Reproduction status

At present, a new reviewer cannot reproduce the complete analysis from this repository alone. To make the project reproducible:

1. Confirm and document the source of the committed CSV plus the licence/redistribution conditions.
2. Add the editable Power BI project (preferably the source-controlled PBIP format) or a report file that meets GitHub's file-size limits.
3. Refresh the model from the included CSV and validate the row count, status totals, date range, and key fields.
4. Test the DAX measures in the actual model, including status and date filter behaviour.
5. Replace provisional figures in the findings register with results recalculated from the source; retain result tables or screenshots that substantiate the findings.

See [the validation checklist](Documentation/Validation_Checklist.md) for acceptance checks.

## Definitions and limitations

- **Completion rate:** completed bookings divided by all bookings in the selected filter context.
- **Cancellation rate:** define explicitly whether the numerator includes customer cancellations only, driver cancellations only, or both. Do not combine cancellations with no-driver-found and incomplete bookings without labelling the broader measure as a failure rate.
- **Completed revenue:** booking value associated with completed bookings, subject to verification of the source's revenue semantics.
- **Revenue per kilometre:** completed revenue divided by distance for completed bookings only.
- **Ratings and time measures:** verify the source definition, unit, and missing-value meaning before interpretation.
- **Association is not causation:** time-of-day, location, vehicle, or rating patterns do not prove why an outcome occurred.

## Licensing

No software licence or dataset redistribution licence is currently documented in this repository. Do not assume that the underlying dataset can be redistributed. Confirm the terms for both code/documentation and source data before adding a licence file.

---

*Portfolio note: the project is presented transparently as an implementation/documentation project until its editable report and source-data validation are available.*
