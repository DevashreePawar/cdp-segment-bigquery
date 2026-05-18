# Customer Data Pipeline – Segment CDP Integration
## Complete Documentation & Architecture Guide

---

## 📊 Project Overview

This is a **production-grade event analytics pipeline** that:
- **Captures** user behavior (product views, signups) via Segment CDP
- **Routes** events to BigQuery data warehouse in real-time
- **Analyzes** visitor behavior, conversion funnels, and acquisition performance
- **Enables** data-driven insights for e-commerce optimization

**Tech Stack:** Segment CDP • BigQuery • SQL • JavaScript

---

## 🏗️ Architecture

```
┌──────────────────┐
│  Your Website    │
│  (JavaScript)    │
└────────┬─────────┘
         │
         │ Events: product_view, user_signup
         │
         ▼
┌──────────────────────┐
│  Segment CDP         │
│  (Data Collection)   │
│  Write Key: l7Er9... │
└────────┬─────────────┘
         │
         │ Batch & Route
         │
         ▼
┌──────────────────────────────────┐
│  Google Cloud Platform           │
│  ┌──────────────────────────────┐│
│  │ BigQuery                     ││
│  │ Project: dulcet-asset-...    ││
│  │ Dataset: segment_events      ││
│  │ Table: events                ││
│  └──────────────────────────────┘│
└────────┬─────────────────────────┘
         │
         │ SQL Queries
         │
         ▼
┌──────────────────┐
│  Analytics &     │
│  Dashboards      │
│  (Business       │
│   Intelligence)  │
└──────────────────┘
```

---

## 📋 Setup Checklist

### ✅ Completed Steps

- [x] **Segment Workspace** - Created "Devashree-Pawar" workspace
- [x] **JavaScript Source** - "E-Commerce Web" source configured
- [x] **BigQuery Project** - Created and billing enabled
- [x] **BigQuery Dataset** - `segment_events` dataset created
- [x] **BigQuery Table** - `events` table with event schema
- [x] **Service Account** - `segment-writer@dulcet-asset-496701-i9.iam.gserviceaccount.com`
- [x] **Segment Destination** - BigQuery destination configured and enabled
- [x] **Source-Destination Link** - E-Commerce Web → BigQuery connected
- [x] **Test Events** - Events routed successfully

---

## 🚀 Implementation Steps

### Step 1: Install Tracking Code (DONE)

See **EVENT_TRACKING_SDK.md** for:
- HTML snippet installation
- React/Vue framework integration
- Vanilla JavaScript examples

### Step 2: Track Events (READY)

```javascript
// Product View
analytics.track('product_view', {
  productId: 'prod_123',
  productName: 'Wireless Headphones',
  productPrice: 99.99,
  productCategory: 'Electronics'
});

// User Signup
analytics.identify('user_123', { email: 'user@example.com' });
analytics.track('user_signup', { 
  signupSource: 'organic_search' 
});
```

### Step 3: Monitor Events (REAL-TIME)

1. **Segment Debugger** - View events in real-time as they're collected
2. **BigQuery Preview** - Check events sync (5-15 min delay)
3. **BigQuery Queries** - Run analytics queries on collected data

---

## 📊 Data Schema

### Events Table Structure

```
segment_events.events
├── event_id (STRING) - Unique event identifier
├── event (STRING) - Event type (product_view, user_signup)
├── userId (STRING) - Your internal user ID
├── anonymousId (STRING) - Browser session ID
├── properties (JSON) - Event-specific data
├── context (JSON) - Page & device context
├── timestamp (TIMESTAMP) - When event occurred
├── received_at (TIMESTAMP) - When Segment received it
├── _loaded_at (TIMESTAMP) - When loaded to BigQuery
├── context_ip (STRING) - User IP address
└── context_user_agent (STRING) - Browser user agent
```

### Event Types

**1. product_view**
```json
{
  "event": "product_view",
  "properties": {
    "productId": "prod_789",
    "productName": "Wireless Headphones",
    "productPrice": 99.99,
    "productCategory": "Electronics",
    "productBrand": "AudioTech",
    "currency": "USD"
  }
}
```

**2. user_signup**
```json
{
  "event": "user_signup",
  "properties": {
    "email": "customer@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "signupSource": "organic_search",
    "signupMethod": "email"
  }
}
```

---

## 🔍 Analytics Queries

See **ANALYTICS_QUERIES.md** for 12 ready-to-use SQL queries:

1. **Events by Type** - Event distribution
2. **Product Views Over Time** - Hourly trends
3. **Top Products** - Most viewed products
4. **Products by Category** - Category performance
5. **Signup Sources** - Acquisition channel analysis
6. **View-to-Signup Conversion** - Funnel metrics
7. **Signup Source + Product Interest** - User behavior by channel
8. **Daily Active Users** - DAU tracking
9. **User Cohorts** - Retention by signup date
10. **Brand/Category Performance** - Competitive analysis
11. **Data Quality Check** - Event validation
12. **Recent Events** - Real-time monitoring

### Quick Query Example

```sql
SELECT
  event,
  COUNT(*) as count,
  COUNT(DISTINCT userId) as unique_users
FROM `dulcet-asset-496701-i9.segment_events.events`
GROUP BY event
ORDER BY count DESC;
```

---

## 🔑 API Keys & Credentials

| Item | Value | Location |
|------|-------|----------|
| **Segment Write Key** | `l7Er9ob0u8GNHa09uRPmExoyCnCgEcNU` | Use in tracking code |
| **GCP Project ID** | `dulcet-asset-496701-i9` | BigQuery project |
| **BigQuery Dataset** | `segment_events` | Data warehouse |
| **BigQuery Table** | `events` | Event data |
| **Service Account** | `segment-writer@...` | Segment authentication |
| **Service Account Email** | `segment-writer@dulcet-asset-496701-i9.iam.gserviceaccount.com` | BigQuery permissions |

---

## 📁 Project Files

```
CDP/
├── SEGMENT_SETUP.md                    # Segment workspace configuration
├── BIGQUERY_SETUP.md                   # BigQuery project setup
├── SERVICE_ACCOUNT_SETUP.md            # GCP service account creation
├── SEGMENT_BIGQUERY_INTEGRATION.md     # Connect Segment to BigQuery
├── CREATE_EVENTS.md                    # How to create and test events
├── ANALYTICS_QUERIES.md                # 12 ready-to-use SQL queries
├── EVENT_TRACKING_SDK.md               # JavaScript tracking code
├── PROJECT_DOCUMENTATION.md            # This file
├── segment-bigquery-496701-11188aa10a0a.json  # Original service account key
└── dulcet-asset-496701-i9-e7cf344b9f7b.json   # Active service account key
```

---

## 🔧 Configuration Reference

### Segment Source Configuration

- **Workspace:** Devashree-Pawar
- **Source Name:** E-Commerce Web
- **Source Type:** JavaScript (Analytics.js)
- **Write Key:** `l7Er9ob0u8GNHa09uRPmExoyCnCgEcNU`

### Segment Destination Configuration

- **Destination Name:** BigQuery Events
- **Destination Type:** BigQuery
- **Project ID:** `dulcet-asset-496701-i9`
- **Dataset:** `segment_events`
- **Table:** `events`
- **Service Account:** (JSON key from GCP)
- **Status:** Active
- **Sync Enabled:** Yes
- **Source Connected:** E-Commerce Web

### BigQuery Configuration

- **Project ID:** `dulcet-asset-496701-i9`
- **Dataset:** `segment_events`
- **Location:** US (or us-east1)
- **Table:** `events`
- **Partitioning:** None (use timestamp for queries)
- **Billing:** Enabled

---

## 🧪 Testing & Verification

### 1. Test Event via cURL

```bash
curl -X POST "https://api.segment.io/v1/track" \
  -H "Content-Type: application/json" \
  -u "l7Er9ob0u8GNHa09uRPmExoyCnCgEcNU:" \
  -d '{
    "userId": "test_user_001",
    "event": "product_view",
    "properties": {
      "productId": "test_prod",
      "productName": "Test Product",
      "productPrice": 99.99
    }
  }'
```

### 2. Verify in Segment Debugger

1. Go to **Segment** → E-Commerce Web source
2. Click **Debugger** tab
3. Send a test event
4. Event should appear in real-time

### 3. Verify in BigQuery

1. Go to **BigQuery** → `segment_events.events` table
2. Click **Preview**
3. Wait 5-15 minutes for sync
4. Event should appear in table

### 4. Run Analytics Query

```sql
SELECT * FROM `dulcet-asset-496701-i9.segment_events.events`
ORDER BY timestamp DESC
LIMIT 10;
```

---

## 🚨 Troubleshooting

### Issue: "Table is empty"

**Solution:**
1. Check Selective Sync in Segment destination - is source enabled?
2. Verify service account has BigQuery Data Editor role
3. Wait 5-15 minutes for initial sync
4. Test with manual SQL INSERT to verify table permissions

### Issue: "Permission denied"

**Solution:**
1. Go to BigQuery dataset → Share Dataset
2. Add service account email with BigQuery Data Editor role
3. Wait 2-3 minutes for permissions to propagate

### Issue: "Invalid Project ID"

**Solution:**
1. Verify project ID in Segment destination
2. Make sure it matches GCP project where dataset exists
3. Check in GCP Console → Billing that project has billing enabled

### Issue: Events not appearing in Debugger

**Solution:**
1. Verify tracking code is installed correctly
2. Check browser console for JavaScript errors
3. Verify Write Key is correct
4. Hard refresh page (Cmd+Shift+R)

---

## 📈 Next Steps & Enhancements

### Immediate

- [x] Verify events are reaching BigQuery (check in 10-15 min)
- [x] Run analytics queries to validate data quality
- [x] Create dashboard from queries (Google Data Studio, Looker, Tableau)

### Short-term

- [ ] Add more events (add_to_cart, purchase, page_view)
- [ ] Implement user trait tracking for cohort analysis
- [ ] Set up scheduled queries for daily reports
- [ ] Create real-time alerts for anomalies

### Medium-term

- [ ] Build ML models on event data
- [ ] Implement predictive analytics (churn, LTV)
- [ ] Create segments for targeted marketing
- [ ] Integrate with marketing automation (email, ads)

### Long-term

- [ ] Federated queries with other data sources
- [ ] Advanced attribution modeling
- [ ] Customer 360 unified profiles
- [ ] Real-time personalization engine

---

## 📞 Support & Resources

### Segment Documentation
- [Segment Docs](https://segment.com/docs/)
- [Analytics.js Reference](https://segment.com/docs/connections/sources/catalog/libraries/website/javascript/)
- [BigQuery Destination](https://segment.com/docs/connections/destinations/catalog/bigquery/)

### Google Cloud Documentation
- [BigQuery Docs](https://cloud.google.com/bigquery/docs)
- [Service Accounts](https://cloud.google.com/iam/docs/service-accounts)
- [BigQuery SQL Reference](https://cloud.google.com/bigquery/docs/reference/standard-sql)

### This Project
- See individual documentation files in CDP/ folder
- Check ANALYTICS_QUERIES.md for query examples
- See EVENT_TRACKING_SDK.md for code examples

---

## 🎯 Success Metrics

Your CDP pipeline is successful when:

1. ✅ Events appear in BigQuery table within 5-15 minutes
2. ✅ Analytics queries return meaningful data
3. ✅ Conversion funnel shows product views → signups
4. ✅ Acquisition channel analysis shows which sources convert best
5. ✅ Real-time dashboard updates with new events

---

## 📝 Summary

You now have a **fully operational Customer Data Pipeline** that:

- **Collects** user events from your website (Segment CDP)
- **Centralizes** data in BigQuery (data warehouse)
- **Analyzes** user behavior with SQL queries
- **Enables** business intelligence and data-driven decisions

This mirrors **enterprise customer-360 systems** used by major retailers and e-commerce platforms!

---

**Status:** ✅ **Production Ready**

**Last Updated:** May 17, 2026  
**Project:** Customer Data Pipeline – Segment CDP Integration  
**Owner:** Devashree-Pawar
