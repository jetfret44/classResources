# SQL Analysis Results - Lab 2

This document details the findings and potential performance considerations for the BigQuery SQL queries developed for time-series analysis, customer cohort analysis, and product association analysis on the `mgmt599-davidjetter-lab2.lab2_dataflow.processed_sales` table (and `mgmt599-davidjetter-lab1.superstore_data.superstore-main` for product associations).

---

## 1. Time-Series Analysis: Sales Aggregations, Moving Averages, and Anomalies

### Query Purpose
To understand sales trends, seasonality, and identify unusual sales events by calculating daily sales, 7-day and 30-day moving averages, and flagging outliers.

### Key Insights (Typical Findings)

*   **Overall Sales Trend:** The 30-day moving average `ma_30_day` often reveals the long-term sales trajectory. For Superstore data, this typically shows a general upward trend over the years, indicating business growth.
*   **Short-Term Fluctuations vs. Trend:** The `ma_7_day` helps smooth out daily noise. Comparing `daily_total_sales` to `ma_7_day` often shows regular weekday/weekend patterns.
*   **Seasonality:**
    *   **Weekly:** Sales are typically lower on weekends (`Saturday`, `Sunday`) and higher mid-week (`Tuesday` to `Thursday`).
    *   **Monthly/Quarterly:** Significant sales spikes are often observed towards the end of the month or quarter, especially in Q4 (October-December), driven by promotions or holiday shopping.
*   **Anomalies:**
    *   **"High Anomaly":** Could indicate successful promotional events, unexpected high demand, or potentially data entry errors (e.g., a very large single order).
    *   **"Low Anomaly":** Might signal stockouts, major external events (holidays, disasters), or operational issues.
    *   These flags highlight periods requiring deeper investigation.

### Performance Considerations

*   **Data Scanned:** The initial `SUM(Sales) GROUP BY sale_date` processes the entire `Order_Date` and `Sales` columns. The window functions for moving averages and standard deviations perform calculations over sorted partitions, which can be computationally intensive but efficient in BigQuery.
*   **Optimization Potential:**
    *   If `processed_sales` table is very large, **partitioning by `Order_Date`** would be highly beneficial. This would allow BigQuery to only scan relevant date partitions when filtering for specific periods, drastically reducing bytes processed.
    *   Ensure `Order_Date` is indeed a `DATE` or `TIMESTAMP` type for efficient date operations.

---

## 2. Customer Cohort Analysis: Retention and LTV

### Query Purpose
To group customers by their acquisition month, track their retention over subsequent months, and understand their purchasing behavior (average purchase value, revenue contribution) as they age within the business.

### Key Insights (Typical Findings)

*   **Cohort Retention:** The `retention_percentage` column is crucial. Early cohorts (e.g., 2014-01) often show a steeper drop-off in the first few months (`cohort_age = 1, 2, 3`), then tend to stabilize at a lower percentage. Newer cohorts can be compared against older ones to assess changes in customer loyalty.
*   **Cohort Size vs. Retention:** Larger cohorts don't necessarily mean better retention. A smaller, well-retained cohort can be more valuable than a large cohort with high churn.
*   **Revenue Contribution:** Observing `revenue_contribution` across `cohort_age` reveals how much a cohort generates over its lifetime. It typically peaks early and then declines as customers churn, but some may show sustained or even growing revenue from loyal customers.
*   **Average Purchase Value by Cohort Age:** `avg_purchase_value_per_customer` can show if customers spend more or less as they become more established. Sometimes, initial purchases are high, then normalize, or loyal customers increase their spend over time.
*   **Problematic Cohorts:** A specific `cohort_month` showing significantly lower retention or revenue contribution compared to adjacent cohorts warrants investigation (e.g., poor marketing campaign, product issues during that month).

### Performance Considerations

*   **`MIN(Order_Date) OVER (PARTITION BY Customer_ID)`:** This is a common and efficient way to find the first purchase.
*   **Multiple Joins and Aggregations:** The query involves several joins and multiple aggregation steps. BigQuery's optimizer is generally good at handling these.
*   **Optimization Potential:**
    *   **Clustering:** If `Customer_ID` is a frequently used join key, clustering the `processed_sales` table by `Customer_ID` could speed up join operations and `DISTINCT` counts.
    *   **Materialized Views:** For complex cohort analysis that is run frequently, a materialized view of the `customer_first_purchase` or `cohort_activity` CTEs could pre-compute results and speed up subsequent queries.

---

## 3. Product Association Analysis: Frequently Bought Together

### Query Purpose
To identify products that are frequently purchased together, quantify the strength of these associations using metrics like Support, Confidence, and Lift, and understand their revenue impact.

### Key Insights (Typical Findings)

*   **High Lift Associations:**
    *   Pairs with `Lift > 1` are bought together more often than expected by chance, indicating a positive association. Very high lift (e.g., >5 or 10) points to strong, perhaps surprising, relationships.
    *   Examples often include complementary products (e.g., "Tables" and "Chairs", "Phones" and "Accessories").
*   **High Confidence Pairs:**
    *   `Confidence_A_to_B` shows how often `B` is bought when `A` is bought. High confidence is useful for recommendation engines (e.g., "Customers who bought Product A also bought Product B with X% confidence").
    *   Be cautious with `Confidence_B_to_A` if `B` is a very common product and `A` is rare; it might be high just because `B` is always bought.
*   **Revenue Impact:** `total_pair_revenue_impact` helps prioritize associations. A pair with high Lift might not have high individual product sales, so its revenue impact might be low. Conversely, a common but low-lift pair (e.g., "Paper" and "Pens") might still contribute significant revenue simply due to volume.
*   **Negative Lift (Lift < 1):** Indicates products that are bought together *less* often than expected by chance (e.g., competing products, products that serve the same purpose).
*   **Practical Examples:** You might find associations like "Copiers" and "Paper," or specific "Office Furnishings" with "Storage."

### Performance Considerations

*   **Self-Join (`product_pairs` CTE):** This is the most computationally expensive part of the query. Joining a table with itself on `order_id` to find all pairs can explode the number of rows if orders have many items. The `op1.product_name < op2.product_name` filter is crucial to avoid duplicate pairs (A,B and B,A) and self-pairs (A,A).
*   **`COUNT(DISTINCT order_id)`:** This operation can be costly on very large datasets, especially within the `pair_metrics` CTE.
*   **Optimization Potential:**
    *   **Filtering `order_products`:** If orders with very few items are not relevant for association (e.g., single-item orders), filter them out early.
    *   **Pre-aggregation:** If `product_name` is long, consider creating a product ID to work with integers for faster joins and comparisons.
    *   **Approximate Counts:** For `COUNT(DISTINCT)`, consider `APPROX_COUNT_DISTINCT` if exact accuracy isn't critical for initial exploration, which can be much faster.
    *   **Top N Pairs First:** The `LIMIT 20` at the end helps, but the calculation still happens for all pairs. If you only need the top N, and the dataset is huge, more advanced techniques (e.g., sampling, or custom UDFs) might be considered, but generally, this structure is standard for association rules.
    *   **Filtering by Support:** Adding a `WHERE pm.num_orders_with_pair > X` in the `pair_metrics` CTE could filter out very rare pairs early, improving performance.

---
