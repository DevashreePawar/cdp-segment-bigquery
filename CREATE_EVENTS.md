# How to Create Events in Segment UI

## Method 1: Create JavaScript Source (Recommended for E-Commerce)

### Step A: Create the Source
1. Log into **Segment** → Go to **Devashree-Pawar** workspace
2. Click **Connections** → **Sources** (left sidebar)
3. Click **Add Source** (blue button, top right)
4. Search for **"JavaScript"** or **"Analytics.js"**
5. Click **Add JavaScript**
6. Name it: `"E-Commerce Web"` or `"Website"`
7. Click **Create Source**
8. Copy your **Write Key** (you'll need this in tracking code)

### Step B: Install Tracking Code on Your Website
Once source is created, you get a snippet. Add to website `<head>`:

```html
<script>
  !function(){var analytics=window.analytics=window.analytics||[];if(!analytics.initialize)if(analytics.invoked)window.console&&console.error&&console.error("Segment snippet included twice.");else{analytics.invoked=!0;analytics.methods=["trackSubmit","trackClick","trackLink","trackForm","pageview","identify","reset","group","track","ready","alias","debug","page","once","off","on","addSourceMiddleware","addIntegrationMiddleware","setAnonymousId","addDestinationMiddleware"];analytics.factory=function(e){return function(){var t=Array.prototype.slice.call(arguments);t.unshift(e);analytics.push(t);return analytics}};for(var e=0;e<analytics.methods.length;e++){var t=analytics.methods[e];analytics[t]=analytics.factory(t)}analytics.load=function(e,t){var n=document.createElement("script");n.type="text/javascript";n.async=!0;n.src="https://cdn.segment.com/analytics.js/v1/"+e+"/analytics.min.js";var a=document.getElementsByTagName("script")[0];a.parentNode.insertBefore(n,a);analytics._loadOptions=t};analytics._writeKey="YOUR_WRITE_KEY_HERE";analytics.SNIPPET_VERSION="4.15.3";analytics.load("YOUR_WRITE_KEY_HERE");analytics.page();}}();
</script>
```

**Replace `YOUR_WRITE_KEY_HERE`** with your actual Write Key from Step A.

---

## Method 2: Send Events via JavaScript Code

Once Analytics.js is loaded on your website, track events like this:

### Tracking Product View Event
```javascript
// When user views a product
analytics.track('product_view', {
  productId: 'prod_789',
  productName: 'Wireless Headphones',
  productPrice: 99.99,
  productCategory: 'Electronics',
  productBrand: 'AudioTech',
  currency: 'USD'
});
```

### Tracking User Signup Event
```javascript
// When user creates account
analytics.identify('user_123', {
  email: 'customer@example.com',
  firstName: 'John',
  lastName: 'Doe'
});

analytics.track('user_signup', {
  signupSource: 'organic_search',
  signupMethod: 'email'
});
```

---

## Method 3: Create Events Using Segment UI (Without Code)

### Step 1: Go to Your Source
1. In Segment → **Connections** → **Sources**
2. Click on your **"E-Commerce Web"** source
3. Go to **Settings** tab

### Step 2: Define Event Schema (Optional, for validation)
1. Click **Edit Schema** (or Events section)
2. Click **+ Add Event**
3. Enter event name: `product_view`
4. Add properties:
   - `productId` (string)
   - `productName` (string)
   - `productPrice` (number)
   - `productCategory` (string)
   - `productBrand` (string)
   - `currency` (string)
5. Click **Save**
6. Repeat for `user_signup` event with properties:
   - `email` (string)
   - `firstName` (string)
   - `lastName` (string)
   - `signupSource` (string)
   - `signupMethod` (string)

---

## Method 4: Test Events Using Segment Debugger

### Step 1: Open Debugger
1. Your source → **Debugger** tab
2. Should see real-time event stream

### Step 2: Send Test Event (via cURL)
In terminal, send test event:

```bash
curl -X POST "https://api.segment.io/v1/track" \
  -H "Content-Type: application/json" \
  -u "YOUR_WRITE_KEY:" \
  -d '{
    "userId": "user_123",
    "event": "product_view",
    "properties": {
      "productId": "prod_789",
      "productName": "Test Product",
      "productPrice": 99.99,
      "productCategory": "Electronics"
    }
  }'
```

### Step 3: Check Debugger
- Switch back to Segment Debugger tab
- You should see your test event appear in real-time

---

## Method 5: Test with Node.js (Backend)

If you have a Node.js backend:

```bash
npm install @segment/analytics-node
```

```javascript
const Analytics = require("@segment/analytics-node");
const client = new Analytics({
  writeKey: "YOUR_WRITE_KEY"
});

// Track product view
client.track({
  userId: "user_123",
  event: "product_view",
  properties: {
    productId: "prod_789",
    productName: "Wireless Headphones",
    productPrice: 99.99
  }
});

// Track signup
client.identify({
  userId: "user_123",
  traits: {
    email: "customer@example.com",
    firstName: "John",
    lastName: "Doe"
  }
});

client.track({
  userId: "user_123",
  event: "user_signup",
  properties: {
    signupSource: "organic_search"
  }
});
```

---

## Quick Summary

| Method | Best For | Complexity |
|--------|----------|-----------|
| **Method 1** | Website tracking | Easy ✅ |
| **Method 2** | Custom tracking code | Medium |
| **Method 3** | Schema definition only | Very Easy ✅ |
| **Method 4** | Quick testing | Easy ✅ |
| **Method 5** | Backend/API events | Medium |

---

## ✅ Checklist to Complete Step 2

- [ ] Create JavaScript source in Segment
- [ ] Get Write Key
- [ ] Add tracking snippet to website OR
- [ ] Use cURL/Postman to test events
- [ ] Check Debugger for incoming events
- [ ] Verify product_view event works
- [ ] Verify user_signup event works

**Next:** Once you see events in Debugger → Set up BigQuery destination
