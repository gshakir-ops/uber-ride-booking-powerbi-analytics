# Source Dataset Notes

## Current repository status

The expected file is named `ncr_ride_bookings.csv` and the transformation guide assumes a booking-level CSV with fields such as booking ID, date, time, booking status, vehicle type, locations, booking value, ride distance, wait-time measures, and ratings.

**The CSV is not included in this repository.** The previous documentation describes it as approximately 150,000 rows and 21 columns for the NCR region of India in 2024. Those details have not been independently verified in this review because the source file and a verifiable source URL are unavailable here.

## Before using the data

1. Record the exact source URL, publisher, dataset version/date, and access date.
2. Confirm attribution and whether the licence permits analysis, publication, and redistribution.
3. Inspect the real CSV headers and data types before applying the M steps in the transformation guide.
4. Confirm row count, unique booking IDs, date range, status categories, and missing-value patterns.
5. Do not assume that a null rating, VTAT, CTAT, or booking value has one specific business meaning without verifying the source documentation.
6. Do not upload identifiable or confidential records.

## Local file location

If you have a licensed copy of the source file, place it at:

`Data/raw/ncr_ride_bookings.csv`

Update the Power Query source path to match your environment. The path used in the guide is an example, not a working path on every computer.

## Source and licensing limitation

A source URL and dataset redistribution terms are not currently recorded in this repository. Until these are documented, the project should not claim that the data is publicly reusable or that its derived results can be independently reproduced by a reviewer.
