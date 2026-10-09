# Key Insights, Provisional Figures, and Validation Status

## Important status note

The source CSV and editable Power BI report are not included in this repository. The numbers below were present in earlier project documentation and have been checked for arithmetic consistency only. They have **not** been recalculated from the underlying CSV in this review and must not be presented as independently validated analytical findings.

Use these figures only as a reconciliation checklist. Replace this section with actual query results after the source data is available and validated.

## 1. Provisional count reconciliation

Earlier project notes reported the following counts:

| Booking outcome/category | Previously documented count | Share of 150,000 reported bookings | Validation status |
|---|---:|---:|---|
| All bookings | 150,000 | 100% | Not verified against source |
| Completed | 93,000 | 62% | Not verified against source |
| Driver cancellations | 27,000 | 18% | Not verified against source |
| Customer cancellations | 10,500 | 7% | Not verified against source |
| No driver found | 10,500 | 7% | Not verified against source |
| Incomplete rides | 9,000 | 6% | Not verified against source |
| Unsuccessful bookings, implied by total minus completed | 57,000 | 38% | Arithmetic only |

### Arithmetic check

Given the previously documented totals:

- 93,000 / 150,000 = **62%** completion.
- (150,000 - 93,000) / 150,000 = **38%** unsuccessful bookings.
- 27,000 + 10,500 + 10,500 + 9,000 = **57,000** reported unsuccessful bookings.
- 57,000 / 150,000 = **38%**, not 33%.
- Driver cancellations are 27,000 / 150,000 = **18% of all bookings** and 27,000 / 57,000 ≈ **47.4% of the implied unsuccessful bookings**.

This resolves the internal documentation conflict: the 33% aggregate failure-rate claim was inconsistent with the documented counts and has been removed. The arithmetic does not prove that the source data contains these values or that all four failure categories are mutually exclusive. Verify both points from the raw records before publication.

## 2. Findings that must not be stated as facts yet

Earlier text included detailed vehicle-level completion rates, hourly completion rates, cancellation reasons, revenue shares, payment-method shares, rating averages, repeat-customer rates, and time-of-day trends. Those values are not substantiated by an included dataset or reproducible result tables. They have intentionally not been repeated here as confirmed findings.

The following remain **questions to test**, not conclusions:

- Do completion and cancellation rates vary materially by vehicle type, hour, day, or location?
- Are cancellation-reason fields complete and consistently populated, and which categories have the highest counts?
- How do booking value and completed distance relate after excluding unsuccessful bookings?
- Do payment-method shares vary by vehicle type or over time?
- What proportion of customers have more than one booking during the observed period?
- How do ratings differ by segment, and how much missingness affects the comparison?
- What do VTAT and CTAT represent in the source, and what are their units?

## 3. Validation steps before publishing findings

1. Confirm the dataset source, data dictionary, observation period, and redistribution rights.
2. Validate the source row count and unique booking IDs. Record duplicates, blank keys, invalid dates, and unexpected statuses.
3. Reconcile booking outcomes from the row-level status field. Check whether status labels and cancellation flags are consistent and mutually exclusive.
4. Recalculate all counts and percentages from the source CSV. Store the executed query or export the result table used to calculate each published statistic.
5. Define each denominator. Distinguish cancellation rate from broader unsuccessful-booking rate.
6. Check whether booking value on a cancelled/incomplete booking represents revenue, an estimate, a refund, or a non-charge.
7. Confirm the units and source definitions for VTAT, CTAT, distance, booking value, and ratings.
8. Validate DAX measures against independent row-level calculations and test report slicers/relationships.
9. Update the README and this file only after the results match. Include limitations and avoid causal claims unless supported by an appropriate design.

## 4. Recommended reporting format

For each confirmed insight, document:

- **Measure and population:** the exact numerator, denominator, period, and filters.
- **Observed result:** a value recalculated from the included or clearly sourced data.
- **Business interpretation:** what the result may mean, stated proportionately.
- **Limitations:** missingness, sample coverage, metric definitions, and possible confounders.
- **Follow-up:** the next query or operational test needed to validate the interpretation.

Do not use industry benchmarks unless an authoritative source, date, geographic scope, and comparable metric definition are cited. Do not infer profitability from booking volume or revenue alone; cost and margin data are required.
