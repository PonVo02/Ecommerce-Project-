# SQL-Ecommerce-Project-
[Dataset](https://console.cloud.google.com/bigquery?hl=vi&inv=1&invt=Ab1UAQ&project=my-project-sql-464309&ws=!1m5!1m4!4m3!1sbigquery-public-data!2sgoogle_analytics_sample!3sga_sessions_20170801)


This project analyzes an **E-commerce dataset** stored in **Google BigQuery**.   The goal is to practice SQL skills and generate insights about: 
- User behavior and sessions
- Traffic sources 
- Transactions &amp; revenue trends
  
| Field Name                          | Data Type | Description                                                                                                                                                                                                                                                                                 |
|-------------------------------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| fullVisitorId                       | STRING    | The unique visitor ID.                                                                                                                                                                                                                                                                      |
| date                                | STRING    | The date of the session in YYYYMMDD format.                                                                                                                                                                                                                                                 |
| totals                              | RECORD    | This section contains aggregate values across the session.                                                                                                                                                                                                                                  |
| totals.bounces                      | INTEGER   | Total bounces (for convenience). For a bounced session, the value is 1, otherwise it is null.                                                                                                                                                                                               |
| totals.hits                         | INTEGER   | Total number of hits within the session.                                                                                                                                                                                                                                                    |
| totals.pageviews                    | INTEGER   | Total number of pageviews within the session.                                                                                                                                                                                                                                               |
| totals.visits                       | INTEGER   | The number of sessions (for convenience). This value is 1 for sessions with interaction events. The value is null if there are no interaction events in the session.                                                                                                                         |
| totals.transactions                 | INTEGER   | Total number of ecommerce transactions within the session.                                                                                                                                                                                                                                  |
| trafficSource.source                | STRING    | The source of the traffic source. Could be the name of the search engine, the referring hostname, or a value of the utm_source URL parameter.                                                                                                                                                 |
| hits                                | RECORD    | This row and nested fields are populated for any and all types of hits.                                                                                                                                                                                                                      |
| hits.eCommerceAction                | RECORD    | This section contains all of the ecommerce hits that occurred during the session. This is a repeated field and has an entry for each hit that was collected.                                                                                                                                 |
| hits.eCommerceAction.action_type    | STRING    | The action type. E.g. Product detail views = 2, Add product(s) to cart = 3, Remove product(s) = 4, Checkout = 5, Completed purchase = 6, Refund of purchase = 7, Checkout options = 8, Unknown = 0. Includes SQL examples for calculating number of products in product detail views.          |
| hits.product                        | RECORD    | This row and nested fields hold the product information for the hit that contains Enhanced Ecommerce PRODUCT data.                                                                                                                                                                           |
| hits.product.productQuantity        | INTEGER   | The quantity of the product.                                                                                                                                                                                                                                                                |
| hits.product.productRevenue         | INTEGER   | Revenue associated with the product.                                                                                                                                                                                                                                                        |
| hits.product.productSKU             | STRING    | The product SKU (stock keeping unit).                                                                                                                                                                                                                                                       |
| hits.product.v2ProductName          | STRING    | The product name, as supplied by the retailer.                                                                                                                                                                                                                                              |
----
# Query 01: calculate total visit, pageview, transaction for Jan, Feb and March 2017 (order by month)
```SQL
SELECT
  format_date("%Y%m", parse_date("%Y%m%d", date)) as month,
  SUM(totals.visits) AS visits,
  SUM(totals.pageviews) AS pageviews,
  SUM(totals.transactions) AS transactions,
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
WHERE _TABLE_SUFFIX BETWEEN '0101' AND '0331'
GROUP BY 1
ORDER BY 1;
```
| Month  | Visits | Pageviews | Transactions |
|--------|--------|-----------|--------------|
| 201701 | 64,694 | 257,708   | 713          |
| 201702 | 62,192 | 233,373   | 733          |
| 201703 | 69,931 | 259,522   | 993          |

### 🔍 Insights
- Traffic dipped in February (visits & pageviews down) but transactions slightly increased (733 vs. 713).
- March rebounded strongly, with highest visits (69,931) and transactions (993, +39% vs Jan).
- Conversion quality improved over time, not just raw traffic volume.

### 💡 Recommendations
- Investigate February: lower traffic but stable conversions → find what kept purchase intent strong.
- Replicate March’s success (campaigns, promotions, seasonality) to sustain growth.
- Focus on conversion optimization rather than only increasing visits.

---
# Query 02: Bounce rate per traffic source in July 2017 
```SQL
SELECT
    trafficSource.source as source
    ,sum(totals.visits) as total_visits
    ,sum(totals.Bounces) as total_no_of_bounces
    ,(sum(totals.Bounces)/sum(totals.visits))* 100.00 as bounce_rate
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
GROUP BY source
ORDER BY total_visits DESC;
```
| Source                     | Total Visits | Total Bounces | Bounce Rate (%) |
|-----------------------------|--------------|---------------|-----------------|
| google                      | 38400        | 19798         | 51.56           |
| (direct)                    | 19891        | 8606          | 43.27           |
| youtube.com                 | 6351         | 4238          | 66.73           |
| analytics.google.com        | 1972         | 1064          | 53.96           |
| Partners                    | 1788         | 936           | 52.35           |
| m.facebook.com              | 669          | 430           | 64.28           |
| google.com                  | 368          | 183           | 49.73           |
| dfa                         | 302          | 124           | 41.06           |
| sites.google.com            | 230          | 97            | 42.17           |
| facebook.com                | 191          | 102           | 53.40           |
| reddit.com                  | 189          | 54            | 28.57           |
| qiita.com                   | 146          | 72            | 49.32           |
| quora.com                   | 140          | 70            | 50.00           |
| baidu                       | 140          | 84            | 60.00           |
| bing                        | 111          | 54            | 48.65           |
| mail.google.com             | 101          | 25            | 24.75           |
| yahoo                       | 100          | 41            | 41.00           |
| blog.golang.org             | 65           | 19            | 29.23           |
| l.facebook.com              | 51           | 45            | 88.24           |
| groups.google.com           | 50           | 22            | 44.00           |
| t.co                        | 38           | 27            | 71.05           |
| google.co.jp                | 36           | 25            | 69.44           |
| m.youtube.com               | 34           | 22            | 64.71           |
| dealspotr.com               | 26           | 12            | 46.15           |
| productforums.google.com    | 25           | 21            | 84.00           |
| support.google.com          | 24           | 16            | 66.67           |
| ask                         | 24           | 16            | 66.67           |
| int.search.tb.ask.com       | 23           | 17            | 73.91           |
| optimize.google.com         | 21           | 10            | 47.62           |
| docs.google.com             | 20           | 8             | 40.00           |
| lm.facebook.com             | 18           | 9             | 50.00           |
| l.messenger.com             | 17           | 6             | 35.29           |
| duckduckgo.com              | 16           | 14            | 87.50           |
| adwords.google.com          | 16           | 7             | 43.75           |
| google.co.uk                | 15           | 7             | 46.67           |
| sashihara.jp                | 14           | 8             | 57.14           |
| lunametrics.com             | 13           | 8             | 61.54           |
| search.mysearch.com         | 12           | 11            | 91.67           |
| outlook.live.com            | 10           | 7             | 70.00           |
| tw.search.yahoo.com         | 10           | 8             | 80.00           |
| phandroid.com               | 9            | 7             | 77.78           |
| connect.googleforwork.com   | 8            | 5             | 62.50           |
| plus.google.com             | 8            | 2             | 25.00           |
| m.yz.sm.cn                  | 7            | 5             | 71.43           |
| search.xfinity.com          | 6            | 6             | 100.00          |
| google.co.in                | 6            | 3             | 50.00           |
| online-metrics.com          | 5            | 2             | 40.00           |
| hangouts.google.com         | 5            | 1             | 20.00           |
| s0.2mdn.net                 | 5            | 3             | 60.00           |
| google.ru                   | 5            | 1             | 20.00           |
| in.search.yahoo.com         | 4            | 2             | 50.00           |
| googleads.g.doubleclick.net | 4            | 1             | 25.00           |
| away.vk.com                 | 4            | 3             | 75.00           |
| m.sogou.com                 | 4            | 3             | 75.00           |
| m.baidu.com                 | 3            | 2             | 66.67           |
| siliconvalley.about.com     | 3            | 2             | 66.67           |
| getpocket.com               | 3            |               |                 |
| github.com                  | 2            | 2             | 100.00          |
| myactivity.google.com       | 2            | 1             | 50.00           |
| plus.url.google.com         | 2            |               |                 |
| centrum.cz                  | 2            | 2             | 100.00          |
| search.1and1.com            | 2            | 2             | 100.00          |
| amp.reddit.com              | 2            | 1             | 50.00           |
| google.cl                   | 2            | 1             | 50.00           |
| moodle.aurora.edu           | 2            | 2             | 100.00          |
| uk.search.yahoo.com         | 2            | 1             | 50.00           |
| m.sp.sm.cn                  | 2            | 2             | 100.00          |
| au.search.yahoo.com         | 2            | 2             | 100.00          |
| google.it                   | 2            | 1             | 50.00           |
| wap.sogou.com               | 2            | 2             | 100.00          |
| google.co.th                | 2            | 1             | 50.00           |
| msn.com                     | 2            | 1             | 50.00           |
| calendar.google.com         | 2            | 1             | 50.00           |
| google.bg                   | 1            | 1             | 100.00          |
| news.ycombinator.com        | 1            | 1             | 100.00          |
| mx.search.yahoo.com         | 1            | 1             | 100.00          |
| es.search.yahoo.com         | 1            | 1             | 100.00          |
| it.pinterest.com            | 1            | 1             | 100.00          |
| web.facebook.com            | 1            | 1             | 100.00          |
| online.fullsail.edu         | 1            | 1             | 100.00          |
| images.google.com.au        | 1            | 1             | 100.00          |
| search.tb.ask.com           | 1            |               |                 |
| ph.search.yahoo.com         | 1            |               |                 |
| web.mail.comcast.net        | 1            | 1             | 100.00          |
| arstechnica.com             | 1            |               |                 |
| google.com.br               | 1            |               |                 |
| suche.t-online.de           | 1            | 1             | 100.00          |
| kidrex.org                  | 1            | 1             | 100.00          |
| malaysia.search.yahoo.com   | 1            | 1             | 100.00          |
| google.nl                   | 1            |               |                 |
| newclasses.nyu.edu          | 1            |               |                 |
| google.es                   | 1            | 1             | 100.00          |
| earth.google.com            | 1            |               |                 |
| aol                         | 1            |               |                 |
| gophergala.com              | 1            | 1             | 100.00          |
| google.ca                   | 1            |               |                 |
| kik.com                     | 1            | 1             | 100.00          |

### 🔍 Insights
- **Google search** is the main traffic driver (38K+ visits) but has a moderate bounce rate (~52%).  
- **Direct traffic** ranks second (19K visits) with a healthier bounce rate (~43%), showing stronger user intent.  
- **YouTube** drives fewer visits (6K) but has a high bounce rate (~67%), suggesting less engaged users.  
- **Social traffic** from Facebook (m.facebook.com, l.facebook.com) shows very high bounce rates (64–88%), indicating weak conversion potential.  
- Some niche sources (e.g., **reddit.com, mail.google.com**) have lower bounce rates (<30%), meaning fewer users leave immediately.
### 💡 Recommendations
- Improve landing pages for high-bounce sources (YouTube, Facebook).  
- Focus on SEO/SEM to lower Google bounce rate.  
- Invest more in low-bounce, high-quality channels (e.g., Reddit, email).  
---
# Query 3: Revenue by traffic source by week, by month in June 2017
```SQL
WITH revenue_bytime AS
(
    SELECT -- tổng doanh số theo tháng 
    FORMAT_DATE('%Y%m', SAFE.PARSE_DATE('%Y%m%d', date)) AS time 
    ,'Month' AS time_type
    ,trafficSource.`source` AS `source`
    ,SUM(product.productRevenue) / 1000000 AS revenue
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201706*`,
  UNNEST (hits) AS hits,
  UNNEST (hits.product) AS product
  WHERE product.productRevenue IS NOT NULL
  GROUP BY  time, `source`

  UNION ALL

  SELECT -- tổng doanh số theo tuần
    FORMAT_DATE('%Y%W', SAFE.PARSE_DATE('%Y%m%d', date)) AS time
    ,'Week' AS time_type 
    ,trafficSource.`source` AS `source`
    ,SUM(product.productRevenue) / 1000000  AS revenue
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201706*`,
  UNNEST (hits) AS hits,
  UNNEST (hits.product) AS product
  WHERE product.productRevenue IS NOT NULL
  GROUP BY time, `source`
 )
SELECT 
  time_type 
  ,time
  ,`source`
  ,revenue  AS revenue
FROM revenue_bytime 
ORDER BY revenue DESC, time_type, time;
```
| Time Type | Time   | Source             | Revenue     |
|-----------|--------|--------------------|-------------|
| Month     | 201706 | (direct)           | 97333.62    |
| Week      | 201724 | (direct)           | 30908.91    |
| Week      | 201725 | (direct)           | 27295.32    |
| Month     | 201706 | google             | 18757.18    |
| Week      | 201723 | (direct)           | 17325.68    |
| Week      | 201726 | (direct)           | 14914.81    |
| Week      | 201724 | google             | 9217.17     |
| Month     | 201706 | dfa                | 8862.23     |
| Week      | 201722 | (direct)           | 6888.90     |
| Week      | 201726 | google             | 5330.57     |
| Week      | 201726 | dfa                | 3704.74     |
| Month     | 201706 | mail.google.com    | 2563.13     |
| Week      | 201724 | mail.google.com    | 2486.86     |
| Week      | 201724 | dfa                | 2341.56     |
| Week      | 201722 | google             | 2119.39     |
| Week      | 201722 | dfa                | 1670.65     |
| Week      | 201723 | dfa                | 1145.28     |
| Week      | 201723 | google             | 1083.95     |
| Week      | 201725 | google             | 1006.10     |
| Month     | 201706 | search.myway.com   | 105.94      |
| Week      | 201723 | search.myway.com   | 105.94      |
| Month     | 201706 | groups.google.com  | 101.96      |
| Week      | 201725 | mail.google.com    | 76.27       |
| Month     | 201706 | chat.google.com    | 74.03       |
| Week      | 201723 | chat.google.com    | 74.03       |
| Month     | 201706 | dealspotr.com      | 72.95       |
| Week      | 201724 | dealspotr.com      | 72.95       |
| Month     | 201706 | mail.aol.com       | 64.85       |
| Week      | 201725 | mail.aol.com       | 64.85       |
| Week      | 201726 | groups.google.com  | 63.37       |
| Month     | 201706 | phandroid.com      | 52.95       |
| Week      | 201725 | phandroid.com      | 52.95       |
| Month     | 201706 | sites.google.com   | 39.17       |
| Week      | 201725 | groups.google.com  | 38.59       |
| Week      | 201725 | sites.google.com   | 25.19       |
| Month     | 201706 | google.com         | 23.99       |
| Week      | 201725 | google.com         | 23.99       |
| Month     | 201706 | yahoo              | 20.39       |
| Week      | 201726 | yahoo              | 20.39       |
| Month     | 201706 | youtube.com        | 16.99       |
| Week      | 201723 | youtube.com        | 16.99       |
| Month     | 201706 | bing               | 13.98       |
| Week      | 201722 | sites.google.com   | 13.98       |
| Week      | 201724 | bing               | 13.98       |
| Month     | 201706 | l.facebook.com     | 12.48       |
| Week      | 201724 | l.facebook.com     | 12.48       |
### 🔍 Insights
- Direct traffic dominates with the highest revenue (~97K in June, ~30K in peak week).
- Google search is the second biggest driver (~18.7K monthly, ~9K in best week).
- DFA provides moderate but consistent revenue (~8.8K monthly).
- Other channels (mail.google.com, Bing, Yahoo, YouTube, Facebook) contribute small amounts.
### 💡 Recommendations
- Strengthen direct traffic loyalty with promotions and retention campaigns.
- Invest more in Google search (SEO + ads) to boost scalable revenue.
- Test ways to increase smaller channels’ ROI (e.g., email campaigns, partnerships).

---
# Query 04: Average number of pageviews by purchaser type (purchasers vs non-purchasers) in June, July 2017.
```SQL
with 
purchaser_data as(
  select
      format_date("%Y%m",parse_date("%Y%m%d",date)) as month,
      (sum(totals.pageviews)/count(distinct fullvisitorid)) as avg_pageviews_purchase,
  from `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
    ,unnest(hits) hits
    ,unnest(product) product
  where _table_suffix between '0601' and '0731'
  and totals.transactions>=1
  and product.productRevenue is not null
  group by month
),

non_purchaser_data as(
  select
      format_date("%Y%m",parse_date("%Y%m%d",date)) as month,
      sum(totals.pageviews)/count(distinct fullvisitorid) as avg_pageviews_non_purchase,
  from `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
      ,unnest(hits) hits
    ,unnest(product) product
  where _table_suffix between '0601' and '0731'
  and totals.transactions is null
  and product.productRevenue is null
  group by month
)

select
    pd.*,
    avg_pageviews_non_purchase
from purchaser_data pd
full join non_purchaser_data using(month)
order by pd.month;
```
| Month  | Avg. Pageviews (Purchase) | Avg. Pageviews (Non-Purchase) |
|--------|----------------------------|-------------------------------|
| 201706 | 94.02                      | 316.87                        |
| 201707 | 124.24                     | 334.06                        |

### 🔍 Insights
- Purchasers view fewer pages than non-purchasers, but their pageviews increased from 94 → 124 between June and July.
- Non-purchasers remain consistently high (317 → 334), showing more browsing without conversion.
### 💡 Recommendations
- Simplify purchase flow: Reduce steps to help non-purchasers convert earlier.
- Enhance targeting: Focus on July’s rising purchaser engagement to keep momentum.

---
# Query 05: Average number of transactions per user that made a purchase in July 2017
```SQL
select
    format_date("%Y%m",parse_date("%Y%m%d",date)) as month,
    sum(totals.transactions)/count(distinct fullvisitorid) as Avg_total_transactions_per_user
from `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
    ,unnest (hits) hits,
    unnest(product) product
where  totals.transactions>=1
and product.productRevenue is not null
group by month;
```
| Month  | Avg. Transactions per User |
|--------|-----------------------------|
| 201707 | 4.16                        |

### 🔍 Insight
- In July 2017, each user made on average ~4 transactions, showing strong purchase activity.

### 💡 Recommendations
- Maintain this high engagement by offering loyalty rewards.
- Explore ways to upsell/cross-sell to further boost average transactions.

---
# Query 06: Average amount of money spent per session. Only include purchaser data in July 2017
```SQL
select
    format_date("%Y%m",parse_date("%Y%m%d",date)) as month,
    ((sum(product.productRevenue)/sum(totals.visits))/power(10,6)) as avg_revenue_by_user_per_visit
from `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
  ,unnest(hits) hits
  ,unnest(product) product
where product.productRevenue is not null
  and totals.transactions>=1
group by month;
```
| Month  | Avg. Revenue per User per Visit |
|--------|----------------------------------|
| 201707 | 43.86                            |
### 🔍 Insight
- In July 2017, each visit generated on average ~$43.9 revenue per user, indicating strong monetization efficiency.

### 💡 Recommendations
- Sustain high-value visits through targeted promotions.
- Analyze top-converting segments to replicate success across other channels.

  ---
# Query 07: Other products purchased by customers who purchased product "YouTube Men's Vintage Henley" in July 2017. Output should show product name and the quantity was ordered.
```SQL with buyer_list as(
    SELECT
        distinct fullVisitorId  
    FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
    , UNNEST(hits) AS hits
    , UNNEST(hits.product) as product
    WHERE product.v2ProductName = "YouTube Men's Vintage Henley"
    AND totals.transactions>=1
    AND product.productRevenue is not null
)

SELECT
  product.v2ProductName AS other_purchased_products,
  SUM(product.productQuantity) AS quantity
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
, UNNEST(hits) AS hits
, UNNEST(hits.product) as product
JOIN buyer_list using(fullVisitorId)
WHERE product.v2ProductName != "YouTube Men's Vintage Henley"
 and product.productRevenue is not null
 AND totals.transactions>=1
GROUP BY other_purchased_products
ORDER BY quantity DESC;
```
| Product Name                                       | Quantity |
|----------------------------------------------------|----------|
| Google Sunglasses                                  | 20       |
| Google Women's Vintage Hero Tee Black              | 7        |
| SPF-15 Slim & Slender Lip Balm                     | 6        |
| Google Women's Short Sleeve Hero Tee Red Heather   | 4        |
| YouTube Men's Fleece Hoodie Black                  | 3        |
| Google Men's Short Sleeve Badge Tee Charcoal       | 3        |
| YouTube Twill Cap                                  | 2        |
| Recycled Mouse Pad                                 | 2        |
| Red Shine 15 oz Mug                                | 2        |
| Google Doodle Decal                                | 2        |
| Google Men's Short Sleeve Hero Tee Charcoal        | 2        |
| Android Women's Fleece Hoodie                      | 2        |
| 22 oz YouTube Bottle Infuser                       | 2        |
| Android Men's Vintage Henley                       | 2        |
| Android Wool Heather Cap Heather/Black             | 2        |
| Crunch Noise Dog Toy                               | 2        |
| Google Twill Cap                                   | 1        |
| Google Men's Long & Lean Tee Grey                  | 1        |
| Google Men's Long & Lean Tee Charcoal              | 1        |
| Google Men's Vintage Badge Tee Black               | 1        |
| YouTube Custom Decals                              | 1        |
| Four Color Retractable Pen                         | 1        |
| Google Laptop and Cell Phone Stickers              | 1        |
| Google Men's Long Sleeve Raglan Ocean Blue         | 1        |
| Google Men's Bike Short Sleeve Tee Charcoal        | 1        |
| Google 5-Panel Cap                                 | 1        |
| Google Toddler Short Sleeve T-shirt Grey           | 1        |
| Android Sticker Sheet Ultra Removable              | 1        |
| Google Men's Vintage Badge Tee White               | 1        |
| Google Men's 100% Cotton Short Sleeve Hero Tee Red | 1        |
| Android Men's Vintage Tank                         | 1        |
| Android Men's Short Sleeve Hero Tee White          | 1        |
| Android Men's Pep Rally Short Sleeve Tee Navy      | 1        |
| YouTube Men's Short Sleeve Hero Tee Black          | 1        |
| YouTube Women's Short Sleeve Hero Tee Charcoal     | 1        |
| Google Men's Performance Full Zip Jacket Black     | 1        |
| 26 oz Double Wall Insulated Bottle                 | 1        |
| Google Men's Pullover Hoodie Grey                  | 1        |
| YouTube Men's Short Sleeve Hero Tee White          | 1        |
| Google Slim Utility Travel Bag                     | 1        |
| Google Men's Zip Hoodie                            | 1        |
| YouTube Men's Long & Lean Tee Charcoal             | 1        |
| Android Men's Short Sleeve Hero Tee Heather        | 1        |
| YouTube Women's Short Sleeve Tri-blend Badge Tee   | 1        |
| Google Women's Long Sleeve Tee Lavender            | 1        |
| YouTube Hard Cover Journal                         | 1        |
| Android BTTF Moonshot Graphic Tee                  | 1        |
| Google Men's Airflow 1/4 Zip Pullover Black        | 1        |
| Google Men's Performance 1/4 Zip Pullover Heather  | 1        |
| 8 pc Android Sticker Sheet                         | 1        |

### 🔍 Insights
- Google Sunglasses is the clear top seller (20 units), far ahead of other products.
- Apparel items (t-shirts, hoodies, caps) dominate the product mix, showing strong demand for branded clothing.
- Accessories and novelty items (lip balm, mugs, decals, mouse pads) sell in smaller but diverse quantities.

### 💡 Recommendations
- Stock more of high-demand items like Google Sunglasses and Hero Tees.
- Promote mid-tier apparel (hoodies, caps) to boost their sales further.
- Bundle small accessories (lip balm, mugs, stickers) to increase average order value.

---
# Query 08: Calculate cohort map from product view to addtocart to purchase in Jan, Feb and March 2017. For example, 100% product view then 40% add_to_cart and 10% purchase.
```SQL 
WITH product_view AS
(
  SELECT 
    FORMAT_DATE('%Y%m', SAFE.PARSE_DATE('%Y%m%d', date )) AS month
    ,COUNT(product.V2ProductName) AS num_product_view
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`,
  UNNEST (hits) AS hits,
  UNNEST (hits.product) AS product
  WHERE _TABLE_SUFFIX BETWEEN '0101' AND '0331'
    AND hits.eCommerceAction.action_type ='2'
    AND product.V2productName IS NOT NULL
  GROUP BY month
  ORDER BY month
),
  addtocart AS
(
  SELECT 
    FORMAT_DATE('%Y%m', SAFE.PARSE_DATE('%Y%m%d', date )) AS month
    ,COUNT(product.V2ProductName) AS num_addtocart
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`,
  UNNEST (hits) AS hits,
  UNNEST (hits.product) AS product
  WHERE _TABLE_SUFFIX BETWEEN '0101' AND '0331'
  AND hits.eCommerceAction.action_type ='3'
  AND product.V2productName IS NOT NULL
  GROUP BY month 
  ORDER BY month 
),
  purchase AS 
(
  SELECT 
    FORMAT_DATE('%Y%m', SAFE.PARSE_DATE('%Y%m%d', date )) AS month
    ,COUNT(product.V2ProductName) AS num_purchase
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`,
  UNNEST (hits) AS hits,
  UNNEST (hits.product) AS product
  WHERE _TABLE_SUFFIX BETWEEN '0101' AND '0331'
  AND hits.eCommerceAction.action_type ='6'
  AND product.V2productName IS NOT NULL
  AND product.productRevenue IS NOT NULL
  GROUP BY month
  ORDER BY month 
)

SELECT 
  a.month
  ,p.num_product_view
  ,a.num_addtocart
  ,c.num_purchase
  ,ROUND(100*(a.num_addtocart / p.num_product_view), 2) AS add_to_cart_rate
  ,ROUND(100*(c.num_purchase / p.num_product_view), 2) AS purchase_rate
FROM product_view AS p
LEFT JOIN addtocart AS a  ON p.month = a.month
LEFT JOIN purchase AS c ON p.month =c.month 
ORDER BY month ASC;
```
| Month  | Product Views | Add to Cart | Purchases | Add-to-Cart Rate (%) | Purchase Rate (%) |
|--------|---------------|-------------|-----------|-----------------------|-------------------|
| 201701 | 25,787        | 7,342       | 2,143     | 28.47                 | 8.31              |
| 201702 | 21,489        | 7,360       | 2,060     | 34.25                 | 9.59              |
| 201703 | 23,549        | 8,782       | 2,977     | 37.29                 | 12.64             |

### 🔍 Insights
- Conversion efficiency improved steadily: add-to-cart rate rose from 28.5% → 37.3%, purchase rate from 8.3% → 12.6% (Jan → Mar).
- Even with slightly fewer views in Feb–Mar, purchases increased significantly, showing stronger buyer intent.


### 💡 Recommendations
- Optimize product pages further to sustain rising add-to-cart behavior.
- Leverage March tactics (e.g., promotions, UX changes) as best practices for future months.
- Scale campaigns driving high-quality traffic instead of pure volume.
