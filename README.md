# SQL-Ecommerce-Project-
[Dataset](https://console.cloud.google.com/bigquery?hl=vi&inv=1&invt=Ab1UAQ&project=my-project-sql-464309&ws=!1m10!1m4!4m3!1sbigquery-public-data!2sgoogle_analytics_sample!3sga_sessions_20170801!1m4!4m3!1smy-project-sql-464309!2shometest!3shackathon_accessories_sales)

This project analyzes an **E-commerce dataset** stored in **Google BigQuery**.   The goal is to practice SQL skills and generate insights about: - User behavior and sessions - Traffic sources - Transactions &amp; revenue trends
| Field Name           | Data Type | Description                                                                 |
|----------------------|-----------|-----------------------------------------------------------------------------|
| fullVisitorId        | STRING    | The unique visitor ID.                                                      |
| date                 | STRING    | The date of the session in YYYYMMDD format.                                 |
| totals               | RECORD    | Contains aggregate values across the session.                               |
| totals.bounces       | INTEGER   | Total bounces (1 = bounced session, otherwise null).                        |
| totals.pageviews     | INTEGER   | Total number of pageviews within the session.                               |
| totals.transactions  | INTEGER   | Total number of ecommerce transactions within the session.                   |
| trafficSource.source | STRING    | Source of the traffic (e.g. search engine, referrer, or `utm_source`).       |
