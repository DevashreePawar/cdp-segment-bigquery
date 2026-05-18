# Segment CDP Setup Guide

## 1. Create Segment Account & Workspace

### Step 1.1: Sign Up
1. Go to [Segment.com](https://segment.com)
2. Click **Sign Up** and create account
3. Verify email
4. Set up organization name (e.g., "E-Commerce Analytics")

### Step 1.2: Create Workspace
✅ **Already done** - You have workspace "Devashree-Pawar" created
(Workspace Name: "Devashree-Pawar")

---

## 2. Configure Event Schema

### Step 2.1: Event Spec - Product View
Create event with standardized properties:

```json
{
  "event": "product_view",
  "userId": "user_123",
  "anonymousId": "anon_456",
  "properties": {
    "productId": "prod_789",
    "productName": "Wireless Headphones",
    "productPrice": 99.99,
    "productCategory": "Electronics",
    "productBrand": "AudioTech",
    "currency": "USD",
    "timestamp": "2026-05-18T17:54:40Z"
  },
  "context": {
    "page": {
      "url": "https://ecommerce.example.com/products/wireless-headphones",
      "title": "Product - Wireless Headphones"
    },
    "userAgent": "Mozilla/5.0..."
  }
}
```

### Step 2.2: Event Spec - User Signup
```json
{
  "event": "user_signup",
  "userId": "user_123",
  "anonymousId": "anon_456",
  "properties": {
    "email": "customer@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "signupSource": "organic_search",
    "signupMethod": "email",
    "timestamp": "2026-05-18T17:54:40Z"
  },
  "context": {
    "page": {
      "url": "https://ecommerce.example.com/signup",
      "title": "Create Account"
    }
  }
}
```

---

## 3. Create Segment Source

### Step 3.1: Add JavaScript Source
1. In Segment app → **Connections** → **Sources**
2. Click **Add Source**
3. Search for "JavaScript" (or "Analytics.js")
4. Click **Add JavaScript**
5. Give it a name: "E-Commerce Web"
6. Click **Create Source**

### Step 3.2: Get API Key
1. Go to **Settings** on the source
2. Copy **Write Key** (API key)
3. Save this securely - you'll use it in your tracking code

**Example Write Key format:** `123abc456def789ghi`

---

## 4. Configure Traits & Properties Schema

### Step 4.1: User Traits (Analytics Profile)
Define what we track about users:

- `email` (string) - User email
- `firstName` (string) - First name
- `lastName` (string) - Last name
- `userId` (string) - Your internal user ID
- `signupDate` (datetime) - When user created account
- `signupSource` (string) - How they found you (organic_search, paid_ads, etc.)
- `ltv` (number) - Lifetime value ($ spent)
- `totalPurchases` (number) - Number of purchases

### Step 4.2: Event Properties
Already defined in event specs above (product details, signup source, etc.)

---

## 5. Segment Settings

### Step 5.1: Identity Resolution
Go to **Settings** → **Identity Resolution**
- Enable "Track Anonymous Behavior" 
- This lets us collect data before users sign up

### Step 5.2: Data Access
Go to **Settings** → **Data Access**
- Note: You'll need credentials for BigQuery destination (next)

---

## 6. Test Segment Source

### Step 6.1: Send Test Event via Segment Debugger
1. In Segment → Your source → **Debugger** tab
2. You should see a live stream of events (once we send them)
3. Check "Connected Sources" is enabled

### Step 6.2: Using Segment Test API (Optional)
Via cURL to test connectivity:

```bash
curl -X POST "https://api.segment.io/v1/track" \
  -H "Content-Type: application/json" \
  -u "<WRITE_KEY>:" \
  -d '{
    "userId": "user_123",
    "event": "product_view",
    "properties": {
      "productId": "prod_789",
      "productName": "Test Product",
      "productPrice": 99.99
    }
  }'
```

---

## 7. Create BigQuery Destination (Prepare for Next Step)

After Segment setup, you'll configure a **BigQuery Destination** to route events.

### Prerequisite Checklist:
- ✅ Segment workspace created
- ✅ JavaScript source configured
- ✅ Write Key saved
- ✅ Event schema defined (product_view, user_signup)
- ⏳ BigQuery destination (see next guide)

---

## Quick Reference

| Item | Value |
|------|-------|
| Workspace Name | Devashree-Pawar |
| Source Type | JavaScript (Analytics.js) |
| Write Key | `[Saved in Settings]` |
| Events Tracked | product_view, user_signup |
| Destination | BigQuery (coming next) |

---

## Troubleshooting

**Issue:** "No events showing in Debugger"
- Ensure Write Key is correct in tracking code
- Check browser console for errors
- Verify analytics.js loaded

**Issue:** "Write Key not working"
- Regenerate in Segment Settings
- Ensure no typos when copying

**Next:** [BigQuery Setup Guide](./BIGQUERY_SETUP.md)
