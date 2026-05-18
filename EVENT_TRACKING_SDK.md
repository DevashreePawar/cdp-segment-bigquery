# JavaScript Event Tracking SDK

Ready-to-use code to track `product_view` and `user_signup` events in your e-commerce app.

---

## Installation

### Option 1: Add Segment Script to HTML `<head>`

```html
<script>
  !function(){var i="analytics",analytics=window[i]=window[i]||[];if(!analytics.initialize)if(analytics.invoked)window.console&&console.error&&console.error("Segment snippet included twice.");else{analytics.invoked=!0;analytics.methods=["trackSubmit","trackClick","trackLink","trackForm","pageview","identify","reset","group","track","ready","alias","debug","page","screen","once","off","on","addSourceMiddleware","addIntegrationMiddleware","setAnonymousId","addDestinationMiddleware","register"];analytics.factory=function(e){return function(){if(window[i].initialized)return window[i][e].apply(window[i],arguments);var n=Array.prototype.slice.call(arguments);if(["track","screen","alias","group","page","identify"].indexOf(e)>-1){var c=document.querySelector("link[rel='canonical']");n.push({__t:"bpc",c:c&&c.getAttribute("href")||void 0,p:location.pathname,u:location.href,s:location.search,t:document.title,r:document.referrer})}n.unshift(e);analytics.push(n);return analytics}};for(var n=0;n<analytics.methods.length;n++){var key=analytics.methods[n];analytics[key]=analytics.factory(key)}analytics.load=function(key,n){var t=document.createElement("script");t.type="text/javascript";t.async=!0;t.setAttribute("data-global-segment-analytics-key",i);t.src="https://cdn.segment.com/analytics.js/v1/" + key + "/analytics.min.js";var r=document.getElementsByTagName("script")[0];r.parentNode.insertBefore(t,r);analytics._loadOptions=n};analytics._writeKey="l7Er9ob0u8GNHa09uRPmExoyCnCgEcNU";;analytics.SNIPPET_VERSION="5.2.0";
  analytics.load("l7Er9ob0u8GNHa09uRPmExoyCnCgEcNU");
  analytics.page();
  }}();
</script>
```

Place this in your `<head>` tag. This loads Segment analytics.js automatically.

---

### Option 2: Use npm Package (React/Node.js)

```bash
npm install @segment/analytics-next
```

```javascript
import { AnalyticsBrowser } from '@segment/analytics-next'

const analytics = AnalyticsBrowser.load({ 
  writeKey: 'l7Er9ob0u8GNHa09uRPmExoyCnCgEcNU' 
})
```

---

## Track Product View Event

Track when a user views a product:

```javascript
// When user lands on a product page
analytics.track('product_view', {
  productId: 'prod_789',
  productName: 'Wireless Headphones',
  productPrice: 99.99,
  productCategory: 'Electronics',
  productBrand: 'AudioTech',
  currency: 'USD'
});
```

### React Example:

```javascript
import { useEffect } from 'react';
import { analytics } from './analytics'; // Your Segment instance

export function ProductPage({ product }) {
  useEffect(() => {
    // Track product view when component loads
    analytics.track('product_view', {
      productId: product.id,
      productName: product.name,
      productPrice: product.price,
      productCategory: product.category,
      productBrand: product.brand,
      currency: 'USD'
    });
  }, [product.id]);

  return (
    <div>
      <h1>{product.name}</h1>
      <p>${product.price}</p>
      {/* Rest of product page */}
    </div>
  );
}
```

### Vanilla JavaScript Example:

```javascript
// On product page load
document.addEventListener('DOMContentLoaded', function() {
  const productData = {
    productId: document.dataset.productId,
    productName: document.dataset.productName,
    productPrice: parseFloat(document.dataset.productPrice),
    productCategory: document.dataset.productCategory
  };

  analytics.track('product_view', productData);
});
```

---

## Track User Signup Event

Track when a user creates an account:

```javascript
// After successful signup
analytics.identify('user_123', {
  email: 'customer@example.com',
  firstName: 'John',
  lastName: 'Doe'
});

analytics.track('user_signup', {
  email: 'customer@example.com',
  firstName: 'John',
  lastName: 'Doe',
  signupSource: 'organic_search',
  signupMethod: 'email'
});
```

### React Example:

```javascript
import { useState } from 'react';
import { analytics } from './analytics';

export function SignupForm() {
  const [formData, setFormData] = useState({
    email: '',
    firstName: '',
    lastName: ''
  });

  const handleSignup = async (e) => {
    e.preventDefault();
    
    try {
      // Create user in your backend
      const response = await fetch('/api/signup', {
        method: 'POST',
        body: JSON.stringify(formData)
      });
      
      const user = await response.json();
      
      // Identify user in Segment
      analytics.identify(user.id, {
        email: user.email,
        firstName: user.firstName,
        lastName: user.lastName
      });
      
      // Track signup event
      analytics.track('user_signup', {
        email: user.email,
        firstName: user.firstName,
        lastName: user.lastName,
        signupSource: 'direct',
        signupMethod: 'email'
      });
      
      // Redirect to dashboard
      window.location.href = '/dashboard';
    } catch (error) {
      console.error('Signup failed:', error);
    }
  };

  return (
    <form onSubmit={handleSignup}>
      <input
        type="email"
        placeholder="Email"
        value={formData.email}
        onChange={(e) => setFormData({...formData, email: e.target.value})}
        required
      />
      <input
        type="text"
        placeholder="First Name"
        value={formData.firstName}
        onChange={(e) => setFormData({...formData, firstName: e.target.value})}
        required
      />
      <input
        type="text"
        placeholder="Last Name"
        value={formData.lastName}
        onChange={(e) => setFormData({...formData, lastName: e.target.value})}
        required
      />
      <button type="submit">Sign Up</button>
    </form>
  );
}
```

### Vanilla JavaScript Example:

```javascript
document.getElementById('signupForm').addEventListener('submit', function(e) {
  e.preventDefault();
  
  const email = document.getElementById('email').value;
  const firstName = document.getElementById('firstName').value;
  const lastName = document.getElementById('lastName').value;
  
  // Identify user
  analytics.identify('user_' + Date.now(), {
    email: email,
    firstName: firstName,
    lastName: lastName
  });
  
  // Track signup
  analytics.track('user_signup', {
    email: email,
    firstName: firstName,
    lastName: lastName,
    signupSource: 'direct',
    signupMethod: 'email'
  });
});
```

---

## Advanced: Track with Custom User ID

If your app has user authentication, identify users properly:

```javascript
// After user logs in
analytics.identify('user_123', {
  email: 'john@example.com',
  firstName: 'John',
  lastName: 'Doe',
  plan: 'premium',
  signupDate: '2026-01-15'
});

// Now all subsequent events are tied to this user
analytics.track('product_view', { productId: 'prod_123' });
```

---

## Debugging & Testing

### Check Segment Debugger

1. Open your website in browser
2. Open Developer Console (F12)
3. Go to Segment source in Segment UI → **Debugger** tab
4. Perform an action (view product, sign up)
5. You should see events in real-time

### Console Logging

```javascript
// Add debug logging
analytics.on('track', function(message) {
  console.log('📊 Event tracked:', message.event, message.properties);
});
```

### Check if Segment is Loaded

```javascript
console.log(typeof analytics); // Should print "object"
console.log(analytics._writeKey); // Should show your write key
```

---

## Event Properties Reference

### product_view Properties

| Property | Type | Required | Example |
|----------|------|----------|---------|
| productId | string | Yes | `prod_789` |
| productName | string | Yes | `Wireless Headphones` |
| productPrice | number | Yes | `99.99` |
| productCategory | string | No | `Electronics` |
| productBrand | string | No | `AudioTech` |
| currency | string | No | `USD` |

### user_signup Properties

| Property | Type | Required | Example |
|----------|------|----------|---------|
| email | string | Yes | `john@example.com` |
| firstName | string | No | `John` |
| lastName | string | No | `Doe` |
| signupSource | string | No | `organic_search` |
| signupMethod | string | No | `email` |

---

## Best Practices

1. **Identify before track** - Always call `identify()` before tracking events for authenticated users
2. **Use consistent IDs** - Use the same userId across all events
3. **Include context** - Add category, brand, source to help with analysis
4. **Test before deploying** - Use debugger to verify events in development
5. **Handle errors** - Wrap in try/catch in production apps

---

## Troubleshooting

### Events not appearing in Debugger?

- Ensure Segment script is loaded: `console.log(typeof analytics)`
- Check Write Key is correct
- Check browser console for JavaScript errors
- Wait a few seconds and refresh debugger

### Data not reaching BigQuery?

- Wait 5-15 minutes for sync
- Check Segment destination status is "Active"
- Verify e-commerce-web source is enabled in Selective Sync
- Check BigQuery dataset has write permissions

### User identification not working?

- Make sure userId is unique and consistent
- Call `identify()` immediately after user signs up/logs in
- Check email and other traits are strings, not numbers

---

## Full Integration Example (HTML + JavaScript)

```html
<!DOCTYPE html>
<html>
<head>
  <title>E-Commerce Store</title>
  <script>
    !function(){var i="analytics",analytics=window[i]=window[i]||[];if(!analytics.initialize)if(analytics.invoked)window.console&&console.error&&console.error("Segment snippet included twice.");else{analytics.invoked=!0;analytics.methods=["trackSubmit","trackClick","trackLink","trackForm","pageview","identify","reset","group","track","ready","alias","debug","page","screen","once","off","on","addSourceMiddleware","addIntegrationMiddleware","setAnonymousId","addDestinationMiddleware","register"];analytics.factory=function(e){return function(){if(window[i].initialized)return window[i][e].apply(window[i],arguments);var n=Array.prototype.slice.call(arguments);if(["track","screen","alias","group","page","identify"].indexOf(e)>-1){var c=document.querySelector("link[rel='canonical']");n.push({__t:"bpc",c:c&&c.getAttribute("href")||void 0,p:location.pathname,u:location.href,s:location.search,t:document.title,r:document.referrer})}n.unshift(e);analytics.push(n);return analytics}};for(var n=0;n<analytics.methods.length;n++){var key=analytics.methods[n];analytics[key]=analytics.factory(key)}analytics.load=function(key,n){var t=document.createElement("script");t.type="text/javascript";t.async=!0;t.setAttribute("data-global-segment-analytics-key",i);t.src="https://cdn.segment.com/analytics.js/v1/" + key + "/analytics.min.js";var r=document.getElementsByTagName("script")[0];r.parentNode.insertBefore(t,r);analytics._loadOptions=n};analytics._writeKey="l7Er9ob0u8GNHa09uRPmExoyCnCgEcNU";;analytics.SNIPPET_VERSION="5.2.0";
    analytics.load("l7Er9ob0u8GNHa09uRPmExoyCnCgEcNU");
    analytics.page();
  }}();
</script>
</head>
<body>
  <h1>Product: Wireless Headphones</h1>
  <p>Price: $99.99</p>
  <button onclick="trackProductView()">Track View</button>
  
  <h2>Signup</h2>
  <form onsubmit="handleSignup(event)">
    <input type="email" id="email" placeholder="Email" required />
    <input type="text" id="firstName" placeholder="First Name" required />
    <button type="submit">Sign Up</button>
  </form>

  <script>
    function trackProductView() {
      analytics.track('product_view', {
        productId: 'prod_789',
        productName: 'Wireless Headphones',
        productPrice: 99.99,
        productCategory: 'Electronics',
        productBrand: 'AudioTech'
      });
      alert('✅ Product view tracked!');
    }

    function handleSignup(event) {
      event.preventDefault();
      const email = document.getElementById('email').value;
      const firstName = document.getElementById('firstName').value;

      analytics.identify('user_' + Date.now(), {
        email: email,
        firstName: firstName
      });

      analytics.track('user_signup', {
        email: email,
        firstName: firstName,
        signupSource: 'direct'
      });

      alert('✅ Signup tracked!');
    }
  </script>
</body>
</html>
```

---

## Next Steps

1. Add Segment snippet to your website
2. Track product views on product pages
3. Track signups on signup form
4. Monitor events in Segment Debugger
5. Check BigQuery for data in 5-15 minutes
6. Build dashboards from analytics queries

Your CDP pipeline is now ready to track real user behavior! 🚀
