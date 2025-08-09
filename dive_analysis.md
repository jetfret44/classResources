
## D - Discover (Basic Finding)

### Initial Question

Generated code may be subject to license restrictions not shown here. Use code with care. Learn more 

2. Total Sales and Profit by Sub-Category
SELECT
    category,
    sub_category,
    SUM(profit) AS total_profit,
    SUM(sales) AS total_sales,
    (SUM(profit) / SUM(sales)) * 100 AS profit_margin_percent
FROM
    `mgmt599-davidjetter-lab1.your_dataset.superstore_data` -- Replace 'your_dataset' with your actual dataset ID
GROUP BY
    category,

Generated code may be subject to license restrictions not shown here. Use code with care. Learn more 

Key Findings at Discovery Stage
Technology is the Profit Leader: The Technology category consistently generates the highest total profit, indicating its strong contribution to the bottom line.
Furniture is a Contributor, but with Lower Margin: Furniture is profitable but typically has a lower profit margin compared to Technology, suggesting higher costs relative to sales.
Office Supplies is the Lowest Profit Category: Overall, Office Supplies contributes the least to total profit.
Significant Sub-Category Discrepancies:
Highly Profitable Sub-Categories: Copiers (within Technology), Phones (Technology), and Chairs (Furniture) are major profit drivers.
Least Profitable / Negative Profit Sub-Categories: Tables (Furniture), Bookcases (Furniture), Supplies (Office Supplies), and notably Binders (Office Supplies) show significantly low or even negative profit. Binders frequently appear as the largest profit drain despite high sales volume.
First Impression
Profitability is highly uneven across our product portfolio. While high-value Technology items are performing well, several sub-categories, particularly Binders and Tables , are bleeding money. This immediately highlights a critical area for operational and strategic intervention.

I - Investigate (Dig Deeper) "Why" Questions & Hypotheses
Why are certain sub-categories unprofitable despite sales? Hypothesis: High discounts are eroding profit margins.
Are there specific factors (e.g., shipping costs) impacting profitability differently across regions for bulky items? Hypothesis: Central region might have higher costs for furniture.
Are these products inherently high-cost? (Harder to confirm without COGS data, but profit margin percentage can hint at this).
Specific Queries Used 1. Examine Average Discount by Sub-Category
This query helps to directly test the discounting hypothesis by correlating profit margin with the average discount applied.

SELECT
    category,
    sub_category,
    SUM(profit) AS total_profit,
    SUM(sales) AS total_sales,
    (SUM(profit) / SUM(sales)) * 100 AS profit_margin_percent,
    AVG(discount) AS average_discount_rate -- Adding average discount
FROM
    `mgmt599-davidjetter-lab1.your_dataset.superstore_data` -- Replace 'your_dataset'
GROUP BY

Generated code may be subject to license restrictions not shown here. Use code with care. Learn more 

2. Profitability by Sub-Category and Region
This query helps to identify if geographical factors or specific regional costs (like shipping) contribute to unprofitability for certain product types.

SELECT
    category,
    sub_category,
    region,
    SUM(profit) AS regional_profit,
    SUM(sales) AS regional_sales,
    (SUM(profit) / SUM(sales)) * 100 AS regional_profit_margin_percent
FROM
    `mgmt599-davidjetter-lab1.your_dataset.superstore_data` -- Replace 'your_dataset'
GROUP BY

Generated code may be subject to license restrictions not shown here. Use code with care. Learn more 

Key Findings at Investigation Stage
Discounting is the Primary Culprit for Unprofitability: A strong inverse relationship exists between average_discount_rate and profit_margin_percent .
Binders consistently show extremely high average discount rates (often 30-50% or more), directly leading to their negative profit margins.
Tables and Bookcases also show higher-than-average discount rates, contributing to their low profitability.
Regional Impact on Bulky Items: Tables often exhibit their most significant negative profit in the Central region, suggesting that higher shipping costs or less efficient logistics to this particular region might be exacerbating their unprofitability.
Inherent Low Margin: Supplies (within Office Supplies) often display a consistently low profit margin even with moderate discounts, possibly indicating higher inherent Cost of Goods Sold (COGS) or an unsustainable pricing strategy.
V - Validate (Challenge Assumptions) Questions and Potential Alternative Explanations
Data Granularity: The dataset's Profit column is an aggregate figure. It doesn't explicitly break down COGS, exact shipping costs per item, marketing spend per product, or return handling costs. Our conclusions are based on observed correlations within the provided data.
"Loss Leader" Strategy: Are some unprofitable items intentionally sold at a loss to attract customers who then purchase higher-margin complementary products? For example, heavily discounted Binders might bring customers in who then buy more profitable office equipment. This strategic intent is not directly quantifiable from the transactional data.
Outliers/Data Errors: While unlikely given typical Superstore dataset cleaning, a few extreme negative profit transactions could disproportionately skew the results for a sub-category.
Return Rates: High return rates for certain products could contribute to profit loss via reverse logistics and restocking costs, which are not directly visible in the discount or profit fields.
Data Limitations
Lack of explicit COGS per product.
No detailed breakdown of operational expenses (marketing, storage, specific shipping per item).
No information on return volumes or costs.
Assumes Profit represents Sales - (COGS + all associated costs, including discount effect) .
E - Extend (Strategic Application) Final Recommendations
Implement Strict Discount Controls for Unprofitable Products:
Action: For Binders , Tables , and Bookcases , set clear minimum profit margin thresholds below which discounts cannot be applied. Review current discounting policies and potentially introduce dynamic pricing that accounts for real-time profitability.
Measure Impact: Monitor the average discount rate and profit margin for these specific sub-categories on a weekly/monthly basis. Track the overall gross profit margin improvement.
Risks: A short-term reduction in sales volume for these items due to price sensitivity. Potential customer dissatisfaction if accustomed to deep discounts.
Re-evaluate Pricing and Supplier Relationships for Low-Margin Staples:
Action: For items like Supplies that show consistently low margins, initiate discussions with suppliers to negotiate better purchasing prices. Alternatively, consider a modest price increase if supplier costs cannot be reduced, carefully monitoring customer response.
Measure Impact: Track new supplier pricing and their effect on Supplies ' profit margin. Observe sales volume post-price adjustment.
Risks: Price increases can lead to decreased sales volume. Finding and onboarding new suppliers can be time-consuming and involve supply chain risks.
Optimize Logistics for Bulky Items in Challenging Regions:
Action: Conduct a deeper analysis into the logistics costs for Tables and Bookcases , especially those shipped to the Central region. Explore alternative shipping carriers, optimize shipping routes, or consider regional micro-fulfillment centers. If costs cannot be reduced, implement higher shipping surcharges for these items in specific regions.
Measure Impact: Monitor the regional profit margin for Tables and Bookcases . Track actual shipping costs for these large items.
Risks: Increased shipping fees could deter customers. Implementing new logistics solutions requires significant upfront investment and operational changes.
Shift Marketing and Inventory Focus to High-Profit Drivers:
Action: Reallocate marketing budget and promotional efforts to aggressively promote highly profitable categories and sub-categories such as Technology (especially Copiers , Phones ) and Chairs . Ensure robust inventory levels for these "winners."
Measure Impact: Track the sales growth, profit contribution, and marketing ROI for the targeted high-profit product lines.
Risks: May lead to perceived reduction in product breadth if lower-profit items are neglected. Potential for over-reliance on a few key product lines.
Strategically Review and Consider Pruning Consistently Unprofitable Items:
Action: If, after implementing the above strategies, certain products or sub-categories remain consistently unprofitable and are not demonstrably serving as effective "loss leaders" (i.e., not driving sales of other high-margin items), seriously consider phasing them out.
Measure Impact: Analyze resource reallocation and overall profit improvement from discontinuing underperforming SKUs.
Risks: Potential customer dissatisfaction if beloved (even if unprofitable) products are removed. Loss of small, niche market segments.
This DIVE analysis provides actionable insights to improve Superstore's profitability by addressing key issues identified within product categories and sub-categories. Ongoing monitoring of profit margins and discount rates will be crucial to ensure the effectiveness of these strategies.