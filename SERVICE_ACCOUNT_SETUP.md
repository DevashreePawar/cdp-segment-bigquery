# Create Service Account for Segment-BigQuery Integration

## Step 1: Go to Google Cloud Console

1. In your BigQuery console, click the **Google Cloud** logo (top left)
2. Or go to [console.cloud.google.com](https://console.cloud.google.com)
3. Make sure your **Segment-BigQuery** project is selected (top left dropdown)

---

## Step 2: Create Service Account

1. Go to **APIs & Services** → **Credentials** (left sidebar)
2. Click **+ Create Credentials** button
3. Select **Service Account**
4. Fill in:
   - **Service account name**: `segment-writer`
   - **Service account ID**: Auto-filled
   - **Description**: "Service account for Segment BigQuery integration"
5. Click **Create and Continue**

---

## Step 3: Grant BigQuery Permissions

On the next screen (**Grant this service account access to the project**):

1. Click the **Select a role** dropdown
2. Search for: **`BigQuery Data Editor`**
3. Click on **"BigQuery Data Editor"**
4. Click **Continue**
5. Click **Done**

---

## Step 4: Download JSON Key

1. Go back to **APIs & Services** → **Credentials**
2. Under **"Service Accounts"** section, click on **`segment-writer`**
3. Go to the **Keys** tab
4. Click **+ Add Key** → **Create new key**
5. Select **JSON** format
6. Click **Create**
7. **A JSON file downloads automatically** - save it somewhere safe!

---

## Step 5: Note Your Service Account Email

In the service account details page, find and **copy** this email:
```
segment-writer@[your-project-id].iam.gserviceaccount.com
```

You'll need this in the next step.

---

## ✅ What You Now Have

- [ ] Service account created: `segment-writer`
- [ ] JSON key file downloaded and saved
- [ ] Service account email noted
- [ ] BigQuery Data Editor role assigned

**Next Step:** Grant this service account access to the `segment_events` dataset
