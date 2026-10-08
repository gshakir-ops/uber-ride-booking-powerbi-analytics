# Business Questions & Analysis Framework

## Questions Answered by the Ride-Booking Analytics Dashboard

This document maps business questions to specific dashboard pages and visualizations where answers can be found.

---

## Executive-Level Questions

### Q1: What is our overall operational health this month?
**Dashboard Page:** Page 1 - Executive Overview  
**Primary Metrics:**
- Total Bookings (KPI card)
- Completion Rate % (KPI card)
- Total Revenue (KPI card)

**How to Interpret:**
- Completion Rate >65% indicates healthy operations
- <55% signals operational stress requiring investigation
- Revenue should grow month-over-month (use trend line)

**Related Insights:** Monthly booking trend shows trajectory; compare to previous month

---

### Q2: What percentage of bookings successfully complete?
**Dashboard Page:** Page 1 - Executive Overview  
**Visualization:** "Booking Status Distribution" (Donut Chart)  
**Metric:** [Completion Rate %]  

**Business Implication:**
- 62% completion is baseline performance
- Each 1% improvement = 1,500 additional completed rides/month
- Completion rate varies by vehicle type and time of day (see Page 2)

---

### Q3: What is our monthly revenue trend?
**Dashboard Page:** Page 1 - Executive Overview  
**Visualization:** "Revenue Trend" (Line Chart, 12 months)  
**Metrics:** [Completed Revenue], [Total Booking Value]  

**Key Observations:**
- Identify peak revenue months
- Spot seasonal patterns or operational changes
- Compare actual to targets (if available)

---

## Operations Management Questions

### Q4: What times of day see peak demand?
**Dashboard Page:** Page 2 - Operations & Performance  
**Visualization:** "Bookings by Hour of Day" (Column Chart)  
**Metric:** [Total Bookings]  

**Expected Pattern:**
- Morning peak: 8-11 AM (~18% of daily volume)
- Evening peak: 6-9 PM (~22% of daily volume)
- Night trough: 12-5 AM (~5% of daily volume)

**Operational Use:**
- Schedule driver supply to match peaks
- Implement surge pricing during 6-9 PM
- Identify staffing needs for customer support

---

### Q5: Which vehicle types generate most bookings?
**Dashboard Page:** Page 2 - Operations & Performance  
**Visualization:** "Bookings by Vehicle Type" (Bar Chart)  
**Filter:** Use Vehicle Type slicer to toggle focus  

**Distribution (baseline):**
- Auto: 24.9% of bookings
- Go Mini: 19.9%
- Go Sedan: 18.1%
- Bike: 15.0%
- Premier Sedan: 12.1%
- eBike: 7.0%
- Uber XL: 3.0%

**Operational Implication:**
- Auto provides highest volume; ensure sufficient fleet
- Premium vehicles (Premier Sedan, Uber XL) are low-volume luxury segment

---

### Q6: How efficient are our pickup and ride times?
**Dashboard Page:** Page 2 - Operations & Performance  
**Visualizations:**
- "Average VTAT by Hour" (Line Chart)
- "Average CTAT by Hour" (Line Chart)

**Metrics:**
- [Average VTAT] (Vehicle Time at Arrival - pickup wait in seconds)
- [Average CTAT] (Customer Time at Arrival / Ride Duration - in seconds)

**Benchmark Targets:**
- VTAT: <300 seconds (5 minutes) for good service
- CTAT: 600-1200 seconds (10-20 minutes) depending on distance

**Operational Use:**
- Peak hours (6-9 PM) show longer wait times → driver shortage
- Early morning shows faster pickup → excess supply

---

### Q7: What is the distribution of ride distances?
**Dashboard Page:** Page 2 - Operations & Performance  
**Visualization:** "Ride Distance Distribution" (Histogram)  
**Buckets:** 0-2 km, 2-5 km, 5-10 km, 10-20 km, 20+ km  

**Insight:** Most rides are short-distance urban trips; long-distance (20+km) represents minority

**Implication:** Pricing should reflect short-trip economics (high volume, low margin)

---

## Service Quality & Diagnostics Questions

### Q8: Why are customers cancelling rides?
**Dashboard Page:** Page 3 - Cancellations & Service Quality  
**Visualization:** "Top Customer Cancellation Reasons" (Horizontal Bar)  
**Metric:** [Cancelled by Customer]  

**Top Reasons (baseline):**
1. Driver is not moving towards pickup location
2. Long wait time
3. High price estimate
4. Driver not found

**Operational Response:**
- Driver tracking transparency → reduce "not moving" cancellations
- Pre-ride notifications → reduce wait time perception
- Price comparison tools → reduce price-related cancellations

---

### Q9: Why are drivers cancelling?
**Dashboard Page:** Page 3 - Cancellations & Service Quality  
**Visualization:** "Top Driver Cancellation Reasons" (Horizontal Bar)  
**Metric:** [Cancelled by Driver]  

**Top Reasons (baseline):**
1. Personal & car-related issues
2. Passenger no-show
3. Pickup location too far
4. Suspicious passenger activity

**Operational Response:**
- Vehicle maintenance program → reduce vehicle issues
- Passenger rating visibility → reduce suspicious passenger cancellations
- Driver incentives for far pickups → reduce distance-based cancellations

---

### Q10: Where do we have "No Driver Found" problems?
**Dashboard Page:** Page 3 - Cancellations & Service Quality  
**Visualization:** "No Driver Found Rate by Vehicle Type" (Bar Chart)  
**Metric:** [No Driver Rate %]  
**Filter:** By location using Location slicer (if available)

**Insight:** Specific vehicle types or locations may have supply shortage

**Operational Response:**
- Reallocate drivers to shortage areas
- Adjust pricing to attract drivers to underserved locations
- Expand driver base in high-demand areas

---

### Q11: How do driver and customer ratings compare?
**Dashboard Page:** Page 3 - Cancellations & Service Quality  
**Visualizations:**
- "Driver Rating Distribution" (Histogram)
- "Customer Rating Distribution" (Histogram)

**Metrics:**
- [Average Driver Rating] (Customer's rating of driver)
- [Average Customer Rating] (Driver's rating of customer)

**Expected Baseline:**
- Driver average: ~4.2/5.0
- Customer average: ~4.3/5.0
- Correlation: Minimal (ratings reflect overall experience, not just speed)

**Insight:** Consistent 4+ ratings indicate acceptable service quality

---

### Q12: What is our cancellation trend over time?
**Dashboard Page:** Page 3 - Cancellations & Service Quality  
**Visualization:** "Cancellation Rate Trend" (Dual-line Chart)  
**Metrics:**
- [Customer Cancellation Rate %] (Red line)
- [Driver Cancellation Rate %] (Orange line)

**Trend Analysis:**
- Rising cancellations → operational issue (driver shortage, system problems)
- Stable cancellations → consistent operational baseline
- Falling cancellations → improvements working (driver incentives, app improvements)

**Monthly Review:** Track month-over-month cancellation changes

---

## Revenue & Growth Questions

### Q13: Which vehicle types generate highest revenue?
**Dashboard Page:** Page 4 - Revenue & Customer Analytics  
**Visualization:** "Revenue by Vehicle Type" (Horizontal Bar, sorted)  
**Metric:** [Completed Revenue]  

**Expected Pattern:**
- Premier Sedan: Highest per-ride value (25-30% of total revenue)
- Auto/Go Sedan: Medium value, highest volume
- Bike/eBike: Lowest per-ride value (short distance, budget segment)

**Strategic Insight:**
- Premium vehicles drive profitability despite lower volume
- Budget vehicles drive scale and market coverage

---

### Q14: How do payment methods affect revenue?
**Dashboard Page:** Page 4 - Revenue & Customer Analytics  
**Visualization:** "Revenue by Payment Method" (Pie Chart)  
**Metric:** [Completed Revenue]  

**Expected Distribution:**
- UPI: ~32% (digital-native, fastest checkout)
- Cash: ~31% (traditional, untracked but common)
- Debit Card: ~17%
- Credit Card: ~7%
- Uber Wallet: ~8%
- Other: ~5%

**Operational Use:**
- Push digital payments (faster, lower fraud)
- Understand cash payment constraints in market
- Wallet adoption rate indicates loyalty program success

---

### Q15: What is the relationship between ride distance and booking value?
**Dashboard Page:** Page 4 - Revenue & Customer Analytics  
**Visualization:** "Booking Value vs Ride Distance" (Scatter Plot)  
**Axes:**
- X: Ride Distance (km)
- Y: Booking Value ($)

**Expected Relationship:**
- Strong positive correlation (longer rides = higher fare)
- Some outliers (surge pricing, vehicle type variation)
- Linear trend suggests distance-based pricing model

**Pricing Insight:**
- If scattered: Vehicle type or time-of-day effects pricing significantly
- If linear: Pure distance-based pricing model

---

### Q16: What is our revenue per kilometer?
**Dashboard Page:** Page 4 - Revenue & Customer Analytics  
**Metric:** [Revenue per KM] (KPI Card)  

**Calculation:** Completed Revenue ÷ Total Completed Distance

**Benchmark:**
- Higher per-km = premium pricing or longer average rides
- Lower per-km = volume/budget segment or short average rides

**Trend:** Month-over-month change indicates pricing power or customer mix shift

---

### Q17: How many customers are repeat customers?
**Dashboard Page:** Page 4 - Revenue & Customer Analytics  
**Visualization:** "Bookings per Customer Distribution" (Histogram)  
**Buckets:** 1-5 rides, 6-10 rides, 11-20 rides, 21-50 rides, 51+ rides

**Insight:**
- High concentration in 1-5 bin = acquisition-heavy (new customers)
- High concentration in 21+ bin = retention success (loyal customer base)
- Multi-modal distribution = two customer segments (one-time vs repeat)

**Business Implication:**
- [Rides per Customer] = Average rides per unique customer
- If <1.5: Mostly one-time users (acquisition focus needed)
- If >2.0: Strong repeat usage (retention-focused market)

---

### Q18: What drives month-over-month growth?
**Dashboard Page:** Page 1 - Executive Overview  
**Visualizations:**
- Monthly booking trend (identify inflection points)
- Monthly revenue trend (compare to bookings)

**Analysis Questions:**
- Is booking volume growing faster than revenue per booking?
- Are specific vehicle types driving growth?
- Does growth correlate with lower prices (volume play) or higher prices (premium)?

**Strategic Response:**
- Growth from volume → focus on availability and speed
- Growth from premium → focus on service quality and features

---

## Advanced Diagnostic Questions

### Q19: Which times/locations have lowest service quality?
**Dashboard Page:** Page 2 & 3 (cross-reference)  
**Investigation:**
1. Page 2: Identify lowest completion rates by hour/vehicle type
2. Page 3: Check cancellation reasons for those segments
3. Page 2: Review VTAT/CTAT for bottlenecks

**Example Finding:**
- 6-9 PM completion rate drops to 55%
- Driver cancellations spike to 25%
- VTAT increases to 600+ seconds
- → Driver shortage during peak hours

**Recommended Action:** Dynamic surge pricing to attract drivers

---

### Q20: What customer segments have highest lifetime value?
**Dashboard Page:** Page 4  
**Metrics:**
- [Average Booking Value per Customer] (Revenue perspective)
- [Rides per Customer] (Engagement perspective)

**Segmentation (if data available):**
- High-frequency, high-value: Premier vehicle, repeat customers
- High-frequency, low-value: Budget vehicles, regular users
- Low-frequency, high-value: Occasional premium rides
- Low-frequency, low-value: One-time budget customers

**Strategic Focus:** Retain high-frequency users and upgrade low-value frequent users to premium

---

## How to Use This Framework

### Daily Operations Review
1. Check Page 1 KPI cards for status
2. Verify monthly booking/revenue trends
3. Alert if completion rate drops >2% from previous day

### Weekly Deep Dive
1. Review Pages 2-3 for operational metrics
2. Identify any new cancellation reason spikes
3. Check rating distributions for quality issues

### Monthly Strategic Review
1. Analyze all 4 pages
2. Compare month-over-month changes
3. Identify business drivers (price, vehicle mix, demand patterns)
4. Plan next month's operational focus

### Executive Reporting
1. Lead with Page 1 summary metrics
2. Provide context: Page 2 operational challenges, Page 3 quality issues, Page 4 growth drivers
3. Quantify impact: "$X revenue opportunity from reducing X-hour cancellations by 5%"

---

## Creating Custom Analyses

### Using Slicers for Deeper Investigation
**Date Slicer:**
- Compare same week last month (seasonal pattern)
- Compare same day last week (day-of-week effect)
- Isolate specific dates for event correlation

**Vehicle Type Slicer:**
- Compare cancellation rates across vehicle types
- Identify which vehicles have highest revenue efficiency
- Spot quality issues specific to vehicle type

**Location Slicer (if implemented):**
- Identify geographic hotspots for cancellations
- Compare supply-demand by location
- Spot regulatory or infrastructure issues by area

---

## Limitations of This Analysis

1. **No Causation:** Correlation doesn't prove causation
   - High cancellations in evening ≠ evening causes cancellations
   - May reflect driver shortage, demand surge, or data collection timing

2. **Single Year:** Cannot identify multi-year seasonality
   - 2024 patterns may not repeat in 2025
   - No historical baseline for comparison

3. **Missing Context:**
   - No external events (holidays, promotions, competitor actions)
   - No driver incentive or pricing changes tracked
   - No marketing spend correlation

4. **Aggregation:** Rolling up to daily/hourly may hide micro-patterns
   - 6-9 PM average masks hour-by-hour variation
   - Weekly average masks intra-week patterns

---

## Recommended Next Steps

✅ **Quick Wins (This Month)**
- Reduce peak-hour cancellations through driver incentives
- Improve "No Driver Found" areas with surge pricing
- Monitor customer rating trends for quality regression

✅ **Medium-term (This Quarter)**
- Implement location-based analysis for geographic optimization
- Build customer segmentation model for targeted retention
- Correlate external events with demand patterns

✅ **Long-term (This Year)**
- Develop predictive cancellation model
- Build supply-demand forecasting
- Implement cohort analysis for customer lifetime value

---

**Version:** 1.0  
**Last Updated:** October 8, 2024  
**Intended Audience:** Business stakeholders, operations managers, analysts  
**Status:** Ready for production use
