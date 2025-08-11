Lab 2 Summary: Pipeline Analytics
Pipeline Configuration
Pipeline Name: mgmt599-data-pipeline

Source: gs://mgmt599-user-dj-data-lake-2/customer-raw-data/

Destination: mgmt599-user-dj-lab2.lab2_dataflow.processed_sales

Schedule: Daily

Key Technical Achievements
Successfully built Dataflow pipeline processing 10,000 records

Reduced processing time from 10 minutes to automated

Created 7 analytical views for reporting

Business Insights from Pipeline Data
1. Time-Series Analysis
Key Observations:

Overall Trend: The data shows relatively stable daily sales patterns throughout the multi-year period, with most daily totals clustering around the baseline (0 line on the chart)

Volatility Patterns: The chart displays consistent day-to-day fluctuations in sales, which is typical for retail or e-commerce businesses. Most variations appear to fall within a normal range of approximately -10k to +20k from the baseline

Notable Spikes: Several significant positive spikes reaching 20k-30k above baseline, particularly visible around:

Mid-2020 period

Various points in 2021

Late 2021/early 2022

Seasonal or Event-Driven Patterns: The periodic large spikes suggest potential seasonal sales events, promotional periods, or holiday-driven increases in sales volume

Data Stability: Despite the volatility, the data shows no major long-term declining trends, indicating relatively healthy and consistent business performance over the analyzed period

2. Cohort Analysis
Key Observations:

Cohort Size Patterns:

Major spike in cohort size around mid-2020, reaching approximately 50k+ customers

Another significant peak appears in early 2021 at around 40k+

Most other periods show much smaller cohort sizes (5k-15k range)

Retention Trends:

The chart shows varying retention patterns across different cohort months

The large cohorts from 2020-2021 period appear to have substantial retained customer numbers

More recent cohorts (2022) show smaller absolute numbers but this could be due to less time for retention measurement

Business Implications:

The massive growth in 2020 likely reflects pandemic-driven customer acquisition

The sustained activity in 2021 suggests successful retention of the 2020 cohorts

The pattern suggests periods of intensive customer acquisition followed by retention focus

3. Market Basket Analysis
Key Observations:

Product Performance Distribution:

Several standout products with significantly higher metrics, reaching 4k-6k range

Most products cluster in the lower performance range (0-2k)

The distribution suggests a typical Pareto pattern where a few products drive most of the impact

Top Performers:

Several products show particularly strong performance across the measured metrics

The highest bars appear to be concentrated among certain product categories

These top performers likely represent key revenue drivers or popular product combinations

Business Strategy Implications:

Clear identification of which products/combinations drive the most value

Opportunity to focus marketing and inventory on high-performing items

Potential to optimize product recommendations based on these patterns

Impact Analysis: Processing Delays on Decisions
Question: What's the impact of processing delays on decisions?

Key Finding: Processing delays are eliminated through automated pipeline transformation, reducing data-to-insight time from weeks to 15-30 minutes, enabling near real-time business decisions based on properly typed and structured data.

Business Impact: Implement automated daily/hourly reporting and real-time anomaly detection to enable proactive decision-making, dynamic pricing adjustments, and agile marketing responses.

Cost Analysis
Dataflow job: ~$0.15 per run

BigQuery storage: ~$5 per month

Total within university credits: Yes

Challenges & Solutions
Challenge 1: Data Type Issues
Problem: Raw CSV data with string-based dates and numeric fields prevented accurate time-series analysis and financial calculations

Solution: Implemented automated type conversion pipeline using Dataflow to parse dates to DATE type and cast numeric strings to FLOAT/INTEGER, enabling reliable SQL-based analytics

Challenge 2: Manual Processing Bottlenecks
Problem: Manual data processing created bottlenecks and human error in business reporting

Solution: Built automated pipeline with consistent schema mapping and validation checks, reducing processing time from manual workflows to 15-30 minute automated runs

Challenge 3: Unstructured Data Format
Problem: Unstructured data format made complex business analysis (cohort retention, product associations) nearly impossible

Solution: Transformed CSV into structured BigQuery table with enforced schema, enabling advanced SQL queries for customer behavior tracking and cross-selling analysis

Next Steps
Additional Pipelines to Build
Real-time dashboard pipeline for continuous KPI monitoring

Anomaly detection & alerting system with Cloud Functions integration

Customer segmentation pipeline feeding marketing automation platforms

Predictive analytics pipeline using Vertex AI for sales forecasting

Incremental loading pipeline to handle continuous data streams efficiently

Optimizations Identified
Implement schema validation checks using BigQuery INFORMATION_SCHEMA

Add row count verification between source CSV and destination table

Create data profiling queries for MIN/MAX/NULL value monitoring

Build date range validation to ensure logical order relationships

Establish categorical consistency checks for product/region dimensions

Conclusion
The transformation from raw CSV to structured BigQuery data fundamentally changes business capability from reactive reporting to proactive decision-making, directly addressing the core question of processing delay impact on business decisions.