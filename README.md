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
## Query 01: calculate total visit, pageview, transaction for Jan, Feb and March 2017 (order by month)
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
# Insight
Traffic dipped in February 2017 but quickly recovered in March, with transactions showing strong growth (up from 713 in Jan to 993 in Mar).
<img width="671" height="133" alt="Screenshot 2025-09-26 at 19 18 12" src="https://github.com/user-attachments/assets/e988fbf6-fb64-4b22-a6ce-10c95afe5809" />

---
## Query 02: Bounce rate per traffic source in July 2017 
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

# Insights
- **Google search** is the main traffic driver (38K+ visits) but has a moderate bounce rate (~52%).  
- **Direct traffic** ranks second (19K visits) with a healthier bounce rate (~43%), showing stronger user intent.  
- **YouTube** drives fewer visits (6K) but has a high bounce rate (~67%), suggesting less engaged users.  
- **Social traffic** from Facebook (m.facebook.com, l.facebook.com) shows very high bounce rates (64–88%), indicating weak conversion potential.  
- Some niche sources (e.g., **reddit.com, mail.google.com**) have lower bounce rates (<30%), meaning fewer users leave immediately.
# Recommendations
- Improve landing pages for high-bounce sources (YouTube, Facebook).  
- Focus on SEO/SEM to lower Google bounce rate.  
- Invest more in low-bounce, high-quality channels (e.g., Reddit, email).  
---
## Query 3: Revenue by traffic source by week, by month in June 2017
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
