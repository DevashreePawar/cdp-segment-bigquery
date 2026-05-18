# Connect Segment to BigQuery Destination

## Step 1: Prepare Your Service Account JSON Key

Before starting, you need:
- ✅ JSON key file (downloaded from Google Cloud)
- ✅ Service account email (example: `segment-writer@project-id.iam.gserviceaccount.com`)
- ✅ BigQuery Project ID (from your GCP console)
- ✅ Dataset name: `segment_events`
- ✅ Table name: `events`

---

## Step 2: Add BigQuery Destination in Segment

### Step 2.1: Go to Segment Destinations
1. Log into **Segment** → **Devashree-Pawar** workspace
2. Click **Connections** → **Destinations** (left sidebar)
3. Click **Add Destination** button

### Step 2.2: Search for BigQuery
1. Search for **"BigQuery"**
2. Click on **"BigQuery"** (by Segment)
3. Click **Add Destination**

### Step 2.3: Configure BigQuery Destination
1. Give it a name: `"BigQuery Events"` or similar
2. Connect it to your **"E-Commerce Web"** source (the JavaScript source you created)
3. Click **Create Destination**

---

## Step 3: Add BigQuery Credentials

### Step 3.1: Go to Destination Settings
1. Your BigQuery destination should now appear
2. Click on it to open settings
3. Go to the **Settings** tab

### Step 3.2: Fill in BigQuery Configuration

You'll see fields for:

**Project Settings:**
- **Project ID**: Paste your GCP project ID (e.g., `segment-bigquery-abc123`)
- **Dataset**: `segment_events`
- **Table**: `events`

**Authentication:**
- **Service Account JSON**: 
  1. Open your downloaded JSON key file in a text editor
  2. Copy the **entire JSON content**
  3. Paste it into the **Service Account JSON** field in Segment

---

## Step 4: Test the Connection

### Step 4.1: Send Test Event via API
Run this cURL command to send a test event:

```bash
curl -X POST "https://api.segment.io/v1/track" \
  -H "Content-Type: application/json" \
  -u "l7Er9ob0u8GNHa09uRPmExoyCnCgEcNU:" \
  -d '{
    "userId": "test_user_123",
    "event": "product_view",
    "properties": {
      "productId": "prod_789",
      "productName": "Test Headphones",
      "productPrice": 99.99
    }
  }'
```

### Step 4.2: Check BigQuery for Data

Wait 1-2 minutes, then:

1. Go to **BigQuery console**
2. Click on **segment_events** dataset
3. Click on **events** table
4. Click **Preview** tab
5. You should see your test event!

If you see the data ✅ - **Connection successful!**

---

## Step 5: Verify Destination Settings

Go back to Segment destination settings and check:
- ✅ Project ID configured
- ✅ Dataset: `segment_events`
- ✅ Table: `events`
- ✅ Service Account JSON valid
- ✅ Status shows "Connected" or "Active"

---

## Troubleshooting

### Issue: "Invalid Service Account JSON"
- Ensure you copied the **entire JSON file** (including opening `{` and closing `}`)
- No extra spaces or line breaks outside the JSON

### Issue: "Permission Denied"
- Go to BigQuery console
- Click **segment_events** dataset
- Click **Share Dataset**
- Ensure your service account email has **BigQuery Data Editor** role

### Issue: "Table not found"
- Verify dataset name: `segment_events`
- Verify table name: `events`
- Both are case-sensitive

### Issue: No data appearing in BigQuery
- Check Segment debugger for events
- Wait 1-2 minutes for sync
- Check destination logs in Segment

---

## ✅ Checklist

- [ ] BigQuery destination created in Segment
- [ ] Project ID entered
- [ ] Dataset: `segment_events`
- [ ] Table: `events`
- [ ] Service Account JSON pasted and valid
- [ ] Test event sent via cURL
- [ ] Event appears in BigQuery table
- [ ] Connection status shows active/connected

**Next Step:** Write SQL queries to analyze events
