# BigQuery Analytics Queries - Customer Data Pipeline

Run these queries in BigQuery to analyze your Segment events.

**Project ID:** `segment-bigquery-496701`  
**Dataset:** `segment_events`  
**Table:** `events`

---

## 1. Total Events by Type

```sql
SELECT
  event,
  COUNT(*) as event_count,
  COUNT(DISTINCT userId) as unique_users,
  TIMESTAMP_TRUNC(MAX(timestamp), HOUR) as latest_event
FROM `segment-bigquery-496701.segment_events.events`
GROUP BY event
ORDER BY event_count DESC;
```

**Use case:** See which events are most common and understand user engagement distribution.

---

## 2. Product Views Over Time (Hourly)

```sql
SELECT
  TIMESTAMP_TRUNC(timestamp, HOUR) as hour,
  COUNT(*) as views,
  COUNT(DISTINCT userId) as unique_viewers
FROM `segment-bigquery-496701.segment_events.events`
WHERE event = 'product_view'
GROUP BY hour
ORDER BY hour DESC;
```

**Use case:** Track product view trends to identify peak traffic periods.

---

## 3. Top Products Viewed

```sql
SELECT
  JSON_EXTRACT_SCALAR(properties, '$.productName') as product_name,
  JSON_EXTRACT_SCALAR(properties, '$.productId') as product_id,
  COUNT(*) as view_count,
  COUNT(DISTINCT userId) as unique_viewers,
  AVG(CAST(JSON_EXTRACT_SCALAR(properties, '$.productPrice') AS FLOAT64)) as avg_price
FROM `segment-bigquery-496701.segment_events.events`
WHERE event = 'product_view'
  AND properties IS NOT NULL
GROUP BY product_name, product_id
ORDER BY view_count DESC
LIMIT 10;
```

**Use case:** Identify your most popular products and viewer engagement.

---

## 4. Products by Category

```sql
SELECT
  JSON_EXTRACT_SCALAR(properties, '$.productCategory') as category,
  COUNT(*) as total_views,
  COUNT(DISTINCT userId) as unique_viewers,
  ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) as pct_of_total_views
FROM `segment-bigquery-496701.segment_events.events`
WHERE event = 'product_view'
  AND properties IS NOT NULL
GROUP BY category
ORDER BY total_views DESC;
```

**Use case:** See which product categories drive the most engagement.

---

## 5. Signup Sources (Acquisition Channels)

```sql
SELECT
  JSON_EXTRACT_SCALAR(properties, '$.signupSource') as signup_source,
  COUNT(*) as signups,
  ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) as pct_of_total
FROM `segment-bigquery-496701.segment_events.events`
WHERE event = 'user_signup'
  AND properties IS NOT NULL
GROUP BY signup_source
ORDER BY signups DESC;
```

**Use case:** Understand which acquisition channels are most effective.

---

## 6. User Journey: View to Signup Conversion

```sql
WITH user_events AS (
  SELECT
    userId,
    event,
    timestamp,
    ROW_NUMBER() OVER (PARTITION BY userId ORDER BY timestamp) as event_sequence
  FROM `segment-bigquery-496701.segment_events.events`
  WHERE event IN ('product_view', 'user_signup')
)
SELECT
  COUNT(DISTINCT CASE WHEN event = 'product_view' THEN userId END) as users_who_viewed_products,
  COUNT(DISTINCT CASE WHEN event = 'user_signup' THEN userId END) as users_who_signed_up,
  COUNT(DISTINCT CASE 
    WHEN event = 'user_signup' 
    AND userId IN (SELECT DISTINCT userId FROM user_events WHERE event = 'product_view')
    THEN userId 
  END) as users_with_both_events,
  ROUND(100.0 * COUNT(DISTINCT CASE 
    WHEN event = 'user_signup' 
    AND userId IN (SELECT DISTINCT userId FROM user_events WHERE event = 'product_view')
    THEN userId 
  END) / COUNT(DISTINCT CASE WHEN event = 'product_view' THEN userId END), 2) as conversion_rate_pct
FROM user_events;
```

**Use case:** Measure how many product viewers eventually sign up (conversion funnel).

---

## 7. Users by Signup Source & Product Interest

```sql
WITH signup_cohorts AS (
  SELECT
    userId,
    JSON_EXTRACT_SCALAR(properties, '$.signupSource') as signup_source,
    timestamp as signup_time
  FROM `segment-bigquery-496701.segment_events.events`
  WHERE event = 'user_signup'
),
product_views_after_signup AS (
  SELECT
    s.userId,
    s.signup_source,
    COUNT(*) as product_views_after_signup,
    JSON_EXTRACT_SCALAR(p.properties, '$.productCategory') as viewed_category
  FROM signup_cohorts s
  JOIN `segment-bigquery-496701.segment_events.events` p
    ON s.userId = p.userId
    AND p.event = 'product_view'
    AND p.timestamp >= s.signup_time
  WHERE p.properties IS NOT NULL
  GROUP BY s.userId, s.signup_source, viewed_category
)
SELECT
  signup_source,
  viewed_category,
  COUNT(DISTINCT userId) as user_count,
  AVG(product_views_after_signup) as avg_views_per_user
FROM product_views_after_signup
GROUP BY signup_source, viewed_category
ORDER BY user_count DESC;
```

**Use case:** See product interests by acquisition source - helps tailor marketing per channel.

---

## 8. Daily Active Users (DAU)

```sql
SELECT
  TIMESTAMP_TRUNC(timestamp, DAY) as date,
  COUNT(DISTINCT userId) as daily_active_users,
  COUNT(DISTINCT CASE WHEN event = 'product_view' THEN userId END) as users_who_viewed,
  COUNT(DISTINCT CASE WHEN event = 'user_signup' THEN userId END) as users_who_signed_up
FROM `segment-bigquery-496701.segment_events.events`
GROUP BY date
ORDER BY date DESC;
```

**Use case:** Track daily user activity and engagement trends.

---

## 9. User Cohort Analysis: Signup Date

```sql
WITH signup_dates AS (
  SELECT
    userId,
    DATE(timestamp) as signup_date
  FROM `segment-bigquery-496701.segment_events.events`
  WHERE event = 'user_signup'
)
SELECT
  signup_date,
  COUNT(*) as signups,
  COUNT(DISTINCT CASE 
    WHEN userId IN (
      SELECT DISTINCT userId FROM `segment-bigquery-496701.segment_events.events` 
      WHERE event = 'product_view'
    ) THEN userId 
  END) as signups_who_viewed_product,
  ROUND(100.0 * COUNT(DISTINCT CASE 
    WHEN userId IN (
      SELECT DISTINCT userId FROM `segment-bigquery-496701.segment_events.events` 
      WHERE event = 'product_view'
    ) THEN userId 
  END) / COUNT(*), 2) as engagement_rate_pct
FROM signup_dates
GROUP BY signup_date
ORDER BY signup_date DESC;
```

**Use case:** Cohort retention - see if users from different signup dates have different engagement patterns.

---

## 10. Brand/Category Performance

```sql
SELECT
  JSON_EXTRACT_SCALAR(properties, '$.productBrand') as brand,
  JSON_EXTRACT_SCALAR(properties, '$.productCategory') as category,
  COUNT(*) as total_views,
  COUNT(DISTINCT userId) as unique_viewers,
  AVG(CAST(JSON_EXTRACT_SCALAR(properties, '$.productPrice') AS FLOAT64)) as avg_price,
  ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) as market_share_pct
FROM `segment-bigquery-496701.segment_events.events`
WHERE event = 'product_view'
  AND properties IS NOT NULL
  AND JSON_EXTRACT_SCALAR(properties, '$.productBrand') IS NOT NULL
GROUP BY brand, category
ORDER BY total_views DESC;
```

**Use case:** Competitive analysis - see how different brands/categories compare.

---

## 11. Data Quality Check

```sql
SELECT
  event,
  COUNT(*) as total,
  COUNT(CASE WHEN userId IS NULL OR userId = '' THEN 1 END) as missing_userId,
  COUNT(CASE WHEN properties IS NULL THEN 1 END) as missing_properties,
  ROUND(100.0 * COUNT(CASE WHEN userId IS NULL OR userId = '' THEN 1 END) / COUNT(*), 2) as missing_userId_pct
FROM `segment-bigquery-496701.segment_events.events`
GROUP BY event;
```

**Use case:** Validate data quality - ensure events have required fields.

---

## 12. Recent Events (Last 100)

```sql
SELECT
  timestamp,
  event,
  userId,
  JSON_EXTRACT_SCALAR(properties, '$.productName') as product_name,
  JSON_EXTRACT_SCALAR(properties, '$.email') as email,
  properties
FROM `segment-bigquery-496701.segment_events.events`
ORDER BY timestamp DESC
LIMIT 100;
```

**Use case:** Real-time monitoring - see most recent events for debugging or verification.

---

## How to Run These Queries

### In BigQuery UI:
1. Go to **BigQuery** → Click **SQL Query** editor
2. Copy and paste any query
3. Click **Run**
4. Results appear below

### Scheduled Queries (Optional):
1. Click **Schedule query** button
2. Set frequency (daily, weekly, etc.)
3. Save results to a table for dashboards

### Export Results:
1. Click the download icon
2. Export to CSV or Google Sheets

---

## Next Steps

- Create dashboards from these queries (Google Data Studio, Looker, Tableau)
- Set up alerts for anomalies (drop in product views, etc.)
- Build real-time monitoring for conversion funnel
- Extend queries to include revenue data when available

---

## Template for Custom Queries

Use this template for custom analysis:

```sql
SELECT
  -- Your columns
FROM `segment-bigquery-496701.segment_events.events`
WHERE event = 'product_view'  -- Change event type as needed
  AND timestamp >= TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 30 DAY)  -- Last 30 days
  AND properties IS NOT NULL
GROUP BY -- Your grouping
ORDER BY -- Your ordering
LIMIT 1000;
```

Replace:
- `product_view` with your event name
- `30 DAY` with your desired time range
- Column selections with what you want to analyze
