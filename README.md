# WiseTrack SDK GTM Template

This GTM template provides a lightweight, configurable way to integrate the WiseTrack Web SDK into websites via Google Tag Manager. It covers:

- Loading and initializing the WiseTrack SDK with configurable options.
- Manually starting and stopping tracking.
- Sending custom events.
- Sending revenue events.
- Flushing collected data.

---

## Template Overview

### WiseTrack SDK

A unified template that handles all WiseTrack SDK operations through a single tag with multiple action modes.

**Action Modes:**

- **Initialize SDK** - Load and configure the WiseTrack SDK
- **Track Event** - Send custom or revenue events
- **Start Tracking** - Manually start tracking session
- **Stop Tracking** - Manually stop tracking session
- **Flush Data** - Clear all collected data and stop tracking

---

## Action Parameters

### 1. Initialize SDK

Load SDK bundle and initialize the SDK with your configuration.

**Parameters:**

- `Action` Select **Initialize SDK**
- `App Token` _(required)_ - Your unique app token from WiseTrack dashboard.
- `App Version` _(required)_ - Your application version (e.g., `1.0.0`).
- `App Framework` _(required)_ - Your application framework:
  - `react`
  - `vue.js`
  - `angular`
  - `next.js`
  - `nuxt.js`
  - `vanilla JS`
  - `native` (default)

**Advanced Configuration** _(optional)_:

- `Environment` - The environment mode:
  - `production` (default)
  - `sandbox`
- `Log Level` - Set the initial log level:
  - `debug` - Verbose logging (development)
  - `info` - Informational messages
  - `warn` - Warnings only (default)
  - `error` - Errors only (production)
- `Auto Start Tracking` - Checkbox to start tracking automatically after initialization (default: enabled)
- `Tracking Waiting Time` - Delay before starting tracking in seconds (default: `0` - immediate)
- `Custom Device ID` - Optional custom device identifier

> **Note:**
> For best results, attach this tag to a **Page View - Initialization** or **Page View - All Pages** trigger.
> This ensures the SDK loads early in the page lifecycle before other dependent tags execute.

**Example Configuration:**

```
Action: Initialize SDK
App Token: your-app-token-here
App Version: 1.0.0
App Framework: react
Environment: production
Log Level: warn
Auto Start Tracking: ✓
```

---

### 2. Track Event

Track custom or revenue-generating events.

**Parameters:**

- `Action` Select **Track Event**
- `Event Name` _(required)_ - Name of the event (e.g., `signup`, `button_click`, `page_view`, `purchase`)
- `Event Type` _(required)_ - Type of event:
  - `Default` - For non-revenue events (signups, clicks, page views, etc.)
  - `Revenue` - For purchases, subscriptions, or revenue-generating actions

**Revenue Event Parameters** _(when Event Type = Revenue)_:

- `Revenue Amount` _(required)_ - The revenue amount as a number (e.g., `99.99`)
- `Currency Code` _(required)_ - 3-letter ISO 4217 currency code (e.g., `USD`, `EUR`, `IRR`)
  - Available codes: USD, EUR, JPY, GBP, AUD, CAD, CHF, CNY, SEK, NZD, MXN, SGD, HKD, NOK, KRW, TRY, RUB, INR, BRL, ZAR, IRR, AED, IQD, SAR, OMR, BTC, ETH, LTC

**Event Parameters** _(optional)_:

- Add custom key-value pairs as event parameters
- Each key and value must be maximum 50 characters
- Keys must be unique

**Example - Default Event:**

```
Action: Track Event
Event Name: newsletter_signup
Event Type: Default
Event Parameters:
  - source: homepage
  - campaign: summer2024
```

**Example - Revenue Event:**

```
Action: Track Event
Event Name: purchase
Event Type: Revenue
Revenue Amount: 149.99
Currency Code: USD
Event Parameters:
  - product_id: SKU-12345
  - category: electronics
```

---

### 3. Start Tracking

Manually start the tracking session.

**Parameters:**

- `Action` Select **Start Tracking**

**Use Cases:**

- Resume tracking after it was stopped
- Start tracking based on user consent
- Conditional tracking activation

**Example:**

```
Action: Start Tracking
Trigger: Custom Event - consent_granted
```

---

### 4. Stop Tracking

Manually stop the tracking session.

**Parameters:**

- `Action` Select **Stop Tracking**

**Use Cases:**

- Pause tracking temporarily
- Stop tracking when user opts out
- Disable tracking for specific user segments

**Example:**

```
Action: Stop Tracking
Trigger: Custom Event - privacy_mode_enabled
```

---

### 5. Flush Data

Flush all pending events, clear stored data, and stop the tracking session.

**Parameters:**

- `Action` Select **Flush Data**

> **⚠️ Warning:**
> Executing this action will **flush all data collected by the SDK** and **stop the tracking session**.
> To resume tracking or use the SDK again, you must **manually start tracking** after this action.

**Use Cases:**

- Clear all tracking data when user logs out
- Reset SDK state completely
- Privacy compliance requirements (data deletion)

**Example:**

```
Action: Flush Data
Trigger: Custom Event - user_logout
```

---

## How to Import & Install in GTM

### Step 1: Import Template

1. Open your target GTM container
2. In the left menu, choose **Templates** → **New**
3. Click the menu button (⋮) in the top-right corner
4. Choose **Import**
5. Upload the `template.tpl` file
6. Click **Save**

### Step 2: Create Tags

Create separate tags for different actions:

#### Tag 1: Initialize SDK (Required)

```
Tag Type: WiseTrack SDK
Action: Initialize SDK
Trigger: Page View - Initialization (or All Pages)
```

#### Tag 2: Track Events (Optional)

```
Tag Type: WiseTrack SDK
Action: Track Event
Trigger: Custom Event - your_event_trigger
```

#### Tag 3: Manage Tracking (Optional)

```
Tag Type: WiseTrack SDK
Action: Start Tracking / Stop Tracking / Flush Data
Trigger: Custom Event - based on your needs
```

### Step 3: Preview & Test

1. Click **Preview** in GTM
2. Open your website in the preview window
3. Verify in the browser console or network inspector:
   - SDK loads successfully
   - Events are tracked
   - API calls appear in the Network tab
4. Check for any console errors

### Step 4: Publish

1. Once testing is complete, click **Submit**
2. Add a version name and description
3. Click **Publish**

---

## Common Implementation Patterns

### Pattern 1: Basic Setup

```
Tag 1: Initialize SDK
  - Trigger: All Pages
  - Auto Start: ✓

Tag 2: Track Page Views
  - Action: Track Event
  - Event Name: page_view
  - Trigger: All Pages
```

### Pattern 2: Consent-Based Tracking

```
Tag 1: Initialize SDK
  - Trigger: All Pages
  - Auto Start: ✗

Tag 2: Start Tracking
  - Action: Start Tracking
  - Trigger: Custom Event - consent_granted

Tag 3: Stop Tracking
  - Action: Stop Tracking
  - Trigger: Custom Event - consent_revoked
```

### Pattern 3: E-commerce Tracking

```
Tag 1: Initialize SDK
  - Trigger: All Pages

Tag 2: Track Purchase
  - Action: Track Event
  - Event Type: Revenue
  - Event Name: purchase
  - Revenue Amount: {{transactionTotal}}
  - Currency: {{transactionCurrency}}
  - Trigger: Transaction - Order Completed
```

### Pattern 4: User Lifecycle

```
Tag 1: Track Signup
  - Action: Track Event
  - Event Name: user_signup
  - Trigger: Custom Event - signup_completed

Tag 2: Flush on Logout
  - Action: Flush Data
  - Trigger: Custom Event - user_logout
```

---

## Troubleshooting

### SDK Not Loading

- Verify the Initialize SDK tag fires on the correct trigger
- Check browser console for script loading errors
- Ensure CDN URLs are accessible

### Events Not Tracking

- Confirm SDK is initialized before tracking events
- Check that the App Token is correct
- Verify event parameters are valid (max 50 characters)

### Revenue Events Not Working

- Ensure Event Type is set to "Revenue"
- Verify Revenue Amount is a valid number
- Check Currency Code is exactly 3 uppercase letters

---

## Support

For additional support or questions about the WiseTrack SDK GTM Template, please contact the WiseTrack support team or visit the documentation at [WiseTrack.io](https://wisetrack.io).
