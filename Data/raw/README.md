# Source Dataset Notes

## File status

The source CSV is present in this repository at:

`Data/raw/ncr_ride_bookings.csv`

The file uploaded to GitHub is approximately 25.5 MB. The project documentation describes the dataset as roughly 150,000 booking records, 21 fields, and bookings in the NCR region of India during 2024. These record, field, and date-range details have not yet been independently verified against the uploaded file.

## Source and attribution

A commonly referenced matching dataset is [Uber Ride Analytics Dashboard — NCR Ride Bookings on Kaggle](https://www.kaggle.com/datasets/yashdevladdha/uber-ride-analytics-dashboard).

**Please confirm that this is the actual source of the committed CSV before treating it as definitive attribution.** Record the source URL, author/publisher, dataset version or access date, and any required attribution. Check the source's licensing and redistribution terms; public download availability does not automatically mean the file may be rehosted or redistributed on GitHub.

## Before using the data

1. Inspect the actual headers and data types before applying the M steps in the transformation guide.
2. Verify source row count, unique booking IDs, date range, status categories, null patterns, and numeric ranges.
3. Confirm the source definitions and units for VTAT, CTAT, distance, booking value, and ratings.
4. Do not treat a missing rating, time metric, or booking value as zero unless the source documentation supports that treatment.
5. Do not upload confidential or personally identifiable data. If the source licence disallows redistribution, remove the CSV from the public repository and replace it with source/retrieval instructions.

## Local file location

Power Query should point to the CSV on the machine used to refresh the report. Any path shown in the implementation guide is a placeholder and must be updated for the reviewer's local environment.

## Validation status

The CSV upload is confirmed by the repository tree. This does not itself validate the data or prove that the numeric findings in the documentation were recalculated from it. Complete the checks in [the portfolio validation checklist](../../Documentation/Validation_Checklist.md) before presenting the project's findings as verified.
