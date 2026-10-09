# Portfolio Release and Validation Checklist

This checklist records the work needed before the project can be described as reproducible or its numerical findings as validated.

## P0 — Required for a credible portfolio release

- [ ] Add the permitted source CSV or provide exact source/retrieval instructions and a documented schema.
- [ ] Record the source URL, publisher, dataset version/date, attribution, and redistribution terms.
- [ ] Add the editable Power BI project (prefer PBIP for source control) or a shareable PBIX within GitHub's file-size limits.
- [ ] Refresh the model in Power BI Desktop and save the tested project.
- [ ] Recalculate every reported total, rate, distribution, and insight from the actual source file.
- [ ] Reconcile all booking outcomes. Verify that total bookings = completed + all mutually exclusive unsuccessful outcomes.
- [ ] Replace any unsupported or example statistics in documentation with actual result tables or remove them.
- [ ] Correct README paths so they match files that exist in the repository.

## P1 — Model and measure validation

- [ ] Confirm the fact-table grain is one row per booking and check duplicate/blank booking IDs.
- [ ] Confirm six distinct dimension tables are documented: DimDate, DimVehicle, DimPaymentMethod, DimBookingStatus, DimLocation, and DimCustomer.
- [ ] Validate one-to-many relationships, key uniqueness, unmatched foreign keys, and the active/inactive pickup/drop location strategy.
- [ ] Mark the date table and sort month names by a numeric month column; use a year-month field for multi-year datasets.
- [ ] Validate all source column names, data types, null patterns, categorical values, and M transformations against the real CSV.
- [ ] Confirm whether cancellation flags can overlap with Booking Status categories.
- [ ] Confirm the meanings and units of VTAT, CTAT, Ride Distance, Booking Value, Driver Rating, and Customer Rating.
- [ ] Make completed revenue and completed distance use the same completed-booking population.
- [ ] Compare every DAX KPI with an independently calculated result from the source data.
- [ ] Test date, vehicle, payment, status, and location slicers; specifically test the inactive drop-location relationship.
- [ ] Validate average and percentage measures when the filtered population is empty or contains blanks.

## P2 — Presentation and GitHub polish

- [ ] Replace the preview GIF with final screenshots if the visuals have changed, and label screenshots clearly.
- [ ] Include at least one evidence table in the documentation for each headline finding.
- [ ] Use commit messages describing the change.
- [ ] Set a concise repository description and relevant topics in GitHub repository settings.
- [ ] Add a software licence only after deciding the intended licence for original project files; separately confirm dataset terms.
- [ ] Keep secrets, personal data, and non-redistributable source data out of the repository.

## Acceptance criteria

The project is ready to be presented as a validated Power BI analysis only when the editable report opens, refreshes from the documented source, the model and measures pass the checks above, and every published numerical finding can be reproduced. Until then, use the status wording in the README.
