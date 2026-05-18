# BigQuery Setup Guide

## 1. Create GCP Project

### Step 1.1: Go to Google Cloud Console
1. Navigate to [console.cloud.google.com](https://console.cloud.google.com)
2. Sign in with your Google account
3. Click **Select a Project** (top left, next to "Google Cloud")
4. Click **New Project**

### Step 1.2: Create New Project
1. Project name: `CDP-Analytics` or `Segment-BigQuery`
2. Leave organization blank (or select yours)
3. Click **Create**
4. Wait 30 seconds for project to initialize

### Step 1.3: Enable BigQuery API
1. Go to **APIs & Services** → **Library**
2. Search for **"BigQuery API"**
3. Click on **BigQuery API**
4. Click **Enable**

---

## 2. Create BigQuery Dataset

### Step 2.1: Open BigQuery Console
1. In GCP Console, click **BigQuery** (left sidebar or search bar)
2. You'll see a project explorer on the left

### Step 2.2: Create Dataset
1. Click your **project name** on the left
2. Click **Create Dataset** button (or three dots → Create Dataset)
3. Fill in:
   - **Dataset ID**: `segment_events`
   - **Location**: Choose your region (e.g., `us-east1`)
   - Leave other settings as default
4. Click **Create Dataset**

### Step 2.3: Create Events Table
1. Click your new **segment_events** dataset
2. Click **Create Table**
3. Fill in:
   - **Table name**: `events`
   - **Schema**: Click **Edit as text** and paste:

```json
[
  {"name": "event_id", "type": "STRING", "mode": "NULLABLE"},
  {"name": "event", "type": "STRING", "mode": "NULLABLE"},
  {"name": "userId", "type": "STRING", "mode": "NULLABLE"},
  {"name": "anonymousId", "type": "STRING", "mode": "NULLABLE"},
  {"name": "properties", "type": "JSON", "mode": "NULLABLE"},
  {"name": "context", "type": "JSON", "mode": "NULLABLE"},
  {"name": "timestamp", "type": "TIMESTAMP", "mode": "NULLABLE"},
  {"name": "received_at", "type": "TIMESTAMP", "mode": "NULLABLE"},
  {"name": "_loaded_at", "type": "TIMESTAMP", "mode": "NULLABLE"},
  {"name": "context_ip", "type": "STRING", "mode": "NULLABLE"},
  {"name": "context_user_agent", "type": "STRING", "mode": "NULLABLE"}
]
```

4. Click **Create Table**

---

## 3. Create Service Account (For Segment Authentication)

### Step 3.1: Create Service Account
1. Go to **APIs & Services** → **Credentials**
2. Click **Create Credentials** → **Service Account**
3. Fill in:
   - **Service account name**: `segment-writer`
   - **Service account ID**: Auto-filled
4. Click **Create and Continue**

### Step 3.2: Grant BigQuery Permission
1. On the **Grant this service account access to the project** screen:
2. Click **Select a role** dropdown
3. Search and select: **`BigQuery Data Editor`**
4. Click **Continue**
5. Click **Done**

### Step 3.3: Create JSON Key
1. On Credentials page, find your **segment-writer** service account
2. Click on it
3. Go to **Keys** tab
4. Click **Add Key** → **Create new key**
5. Select **JSON** format
6. Click **Create**
7. A JSON file downloads automatically
8. **Save this file securely** - you'll need it for Segment configuration

Your JSON key looks like:
```json
{
  "type": "service_account",
  "project_id": "cdp-analytics-12345",
  "private_key_id": "abc123...",
  "private_key": "-----BEGIN PRIVATE KEY-----...",
  "client_email": "segment-writer@cdp-analytics-12345.iam.gserviceaccount.com",
  "client_id": "123456789",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://oauth2.googleapis.com/token",
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/..."
}
```

---

## 4. Grant Service Account Access to Dataset

### Step 4.1: Edit Dataset Permissions
1. Go to **BigQuery** → **segment_events** dataset
2. Click **Share Dataset** (or three dots → Share)
3. In **Dataset permissions**, click **Add Principal**
4. Paste your service account email: `segment-writer@cdp-analytics-12345.iam.gserviceaccount.com`
5. Select role: **BigQuery Data Editor**
6. Click **Save**

---

## 5. Get BigQuery Connection Details

You'll need these for Segment configuration:

| Item | Value |
|------|-------|
| **Project ID** | `cdp-analytics-12345` (from JSON key) |
| **Dataset** | `segment_events` |
| **Table** | `events` |
| **Service Account Email** | `segment-writer@cdp-analytics-12345.iam.gserviceaccount.com` |
| **JSON Key** | (downloaded file) |

---

## 6. Quick Test (Optional)

### Test BigQuery Query
1. Go to **BigQuery** → **SQL Query** editor
2. Run:
```sql
SELECT COUNT(*) as row_count FROM `cdp-analytics-12345.segment_events.events`;
```
(Replace `cdp-analytics-12345` with your actual project ID)

3. You should see `row_count: 0` (table is empty, which is expected)

---

## ✅ Checklist

- [ ] GCP Project created
- [ ] BigQuery API enabled
- [ ] `segment_events` dataset created
- [ ] `events` table created with schema
- [ ] Service account created (`segment-writer`)
- [ ] Service account has BigQuery Data Editor role
- [ ] JSON key downloaded and saved
- [ ] Service account has access to dataset
- [ ] Have Project ID, Dataset ID, Table name

**Next:** Configure Segment BigQuery Destination
