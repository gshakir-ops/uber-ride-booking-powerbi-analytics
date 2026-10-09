# Business Questions and Analysis Plan

## Purpose and status

This document defines questions that a ride-booking analytics report could investigate. It is an analysis plan, **not a list of proven results**. The underlying CSV and editable Power BI report are not included in this repository, so no numerical pattern, benchmark, or causal explanation below should be assumed to have been observed.

The proposed page names are a suggested layout. Confirm that each visual and measure exists in the final report before describing it as implemented.

## 1. Executive overview

| Business question | Suggested measure or visual | Validation notes |
|---|---|---|
| How many booking attempts are recorded? | Total bookings KPI | Reconcile with the source row count and unique booking IDs |
| What share of bookings complete? | Completion rate KPI and outcome distribution | Completed bookings / all bookings in the same filter context |
| How many bookings are cancelled? | Customer and driver cancellation counts/rates | Keep customer, driver, and overall cancellation definitions distinct |
| How is completed-booking revenue changing over time? | Monthly completed-revenue trend | Confirm booking-value semantics and date coverage |
| Are bookings and revenue moving in the same direction? | Monthly booking and revenue trends | Do not infer cause from coincident trends |

## 2. Operations and demand

| Business question | Suggested measure or visual | Validation notes |
|---|---|---|
| When is booking volume highest? | Bookings by hour and day of week | Check date/time parsing, missing times, and sample coverage |
| Do completion rates vary by hour or day? | Completion rate by hour/day | Compare rates, not only raw counts |
| Do outcomes differ by vehicle type? | Bookings and outcome rates by vehicle type | Confirm consistent categories and adequate group sizes |
| Do pickup wait-time metrics vary by segment? | Average VTAT by hour/vehicle type | Verify source definition, unit, and blank handling first |
| Do CTAT values vary by segment? | Average CTAT by hour/vehicle type | Verify the exact source definition; do not automatically label it ride duration |
| Which pickup areas have higher booking volume or unsuccessful rates? | Pickup-location volume and outcomes | Use the pickup-location relationship and confirm blank/unknown locations |
| Do common routes show different outcomes? | Pickup-to-drop route analysis | Validate location keys and manage high-cardinality routes carefully |

## 3. Cancellations and service quality

| Business question | Suggested measure or visual | Validation notes |
|---|---|---|
| How many bookings are cancelled by customers vs drivers? | Separate count and rate measures | Verify that each booking belongs to one status category or document overlap |
| Which recorded cancellation reasons are most common? | Reason counts by responsible party | Only report reasons present in the source; do not infer them from unrelated patterns |
| Where is “no driver found” most frequent? | Count/rate by hour, vehicle, and location | Define denominator and confirm the status is distinct from cancellations |
| How common are incomplete rides? | Incomplete count/rate over time and by segment | Confirm exact status definition and any overlap with cancellation flags |
| What are the observed rating distributions? | Driver/customer rating histograms and averages | Exclude blanks, show rating coverage, and avoid treating missing ratings as zero |
| Are ratings associated with time or outcome? | Segmented comparison | Describe association only; missing ratings on unsuccessful bookings can bias comparisons |

## 4. Revenue and customer behaviour

| Business question | Suggested measure or visual | Validation notes |
|---|---|---|
| Which vehicle types contribute the most completed revenue? | Completed revenue by vehicle type | Revenue share is not profitability; costs/margins are not documented |
| Which payment methods are used most often? | Booking count and completed revenue by payment method | Check missing/unknown values and avoid assuming payment preference explains outcomes |
| How does booking value relate to distance? | Scatter plot and summary statistics | Check units, outliers, completed-vs-unsuccessful bookings, and vehicle mix |
| What is revenue per kilometre for completed rides? | Completed revenue / completed distance | Both numerator and denominator must use the completed-booking population |
| How often do customers book more than once? | Distribution of bookings per customer | This is repeat usage during the observed window, not lifetime value or retention |
| Does average booking value vary by time or vehicle? | Average completed value by segment | State whether this uses all bookings or completed bookings only |

## 5. Suggested report structure

1. **Executive overview:** booking volume, outcome rates, and completed revenue.
2. **Operations:** time, vehicle, and location comparisons.
3. **Cancellations and service quality:** cancellation categories, recorded reasons, incomplete bookings, ratings.
4. **Revenue and customer behaviour:** completed revenue, distance/value, payment methods, booking frequency.

This is a recommended structure, not confirmation that these four pages currently exist in an editable report.

## 6. Reporting rules

- Define every KPI in a visible measure reference, including numerator and denominator.
- Separate cancellation rate from total unsuccessful-booking rate.
- Do not report example values, thresholds, benchmark claims, or predicted impacts as actual outcomes.
- Cite an authoritative source for any external benchmark, including date, geography, and a comparable definition.
- Do not claim profitability without cost or margin data.
- Do not claim that an operational factor caused an outcome from descriptive comparisons alone.
- Show the number of observations and missing-value coverage when interpreting averages or rating distributions.
- Reconcile report totals to the source file before presenting findings to a hiring manager.
