# WiseTrack SDK – Google Tag Manager Template

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![GTM](https://img.shields.io/badge/Google%20Tag%20Manager-Community%20Template-246FDB.svg)](https://tagmanager.google.com/gallery/)
[![WiseTrack SDK](https://img.shields.io/npm/v/wisetrack?label=wisetrack%20sdk)](https://www.npmjs.com/package/wisetrack)

The official Google Tag Manager tag template for the [WiseTrack](https://www.wisetrack.io) Web SDK.
It lets you install WiseTrack on any website and use all of its web features from GTM, without
changing your site's code.

## Contents

- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Quick start](#quick-start)
- [Actions](#actions)
  - [Initialize SDK](#initialize-sdk)
  - [Track Event](#track-event)
  - [Track Screen](#track-screen)
  - [Start Tracking / Stop Tracking](#start-tracking--stop-tracking)
  - [Set Push Notification Token (FCM)](#set-push-notification-token-fcm)
  - [Set Log Level](#set-log-level)
  - [Enable / Disable SDK](#enable--disable-sdk)
  - [Flush](#flush)
- [Deeplinks in the dataLayer](#deeplinks-in-the-datalayer)
- [Recipes](#recipes)
- [SDK loading and compatibility](#sdk-loading-and-compatibility)
- [Permissions](#permissions)
- [Troubleshooting](#troubleshooting)
- [Support](#support)
- [License](#license)

## Features

- **Initialization.** Loads the WiseTrack Web SDK from a CDN and initializes it with your app token, client secret, environment and log level.
- **Screen tracking.** Tracks page views automatically, including route changes in single-page apps. Native `<dialog>` elements are tracked as dialog screens. You can exclude paths and URL patterns and strip sensitive query parameters.
- **Event tracking.** Tracks custom events and revenue events, with optional parameters.
- **Manual screen views.** Track Screen records views that do not change the URL, such as tabs, steps, bottom sheets and overlays.
- **Deeplinks.** The SDK handles WiseTrack deeplinks and deferred deeplinks. The tag can push each one to the `dataLayer` so other tags and triggers can use it.
- **Push notifications.** Sends the browser's Firebase Cloud Messaging (FCM) token to WiseTrack.
- **Runtime control.** Start or stop tracking (for example for consent), enable or disable the SDK, change the log level, and flush stored data.

## Requirements

- A WiseTrack account and an app created in the WiseTrack panel.
- The app's **App Token** and **Client Secret**, both shown in the WiseTrack panel.
- A **Web** container in Google Tag Manager.

The template loads the latest WiseTrack Web SDK itself, so nothing needs to be installed on the
site. If your site already loads the SDK, see [SDK loading and compatibility](#sdk-loading-and-compatibility).

## Installation

### From the Community Template Gallery (recommended)

1. In your GTM container, open **Templates**.
2. Under **Tag Templates**, click **Search Gallery**.
3. Search for **WiseTrack SDK** and click **Add to workspace**.
4. Review the permissions and click **Add**.

Templates added from the Gallery show a notice in GTM when a new version is available.

### Manual import

1. Download [`template.tpl`](template.tpl) from this repository.
2. In GTM, go to **Templates** → **Tag Templates** → **New**.
3. Open the menu (⋮) in the top-right corner and choose **Import**.
4. Select `template.tpl`, then click **Save**.

## Quick start

1. **Create the initialization tag.**
   - Tag type: **WiseTrack SDK**.
   - Action: **Initialize SDK**.
   - Fill in **App Token**, **Client Secret** and **App Version**, and choose your **App Framework**.
   - Trigger: **Initialization - All Pages**.
2. **Create a tag for each event you want to track.**
   - Tag type: **WiseTrack SDK**.
   - Action: **Track Event**.
   - Event Name: the name defined in the WiseTrack panel, for example `signup`.
   - Trigger: the trigger for that event, for example a *Custom Event* or a *Click* trigger.
3. **Test in Preview mode.** Click **Preview**, open your site, and check that the tags fire. Set **Environment** to **Sandbox** and **Log Level** to **Debug** while testing.
4. **Publish.** Before you publish, set **Environment** back to **Production**.

> **Tag order:** All actions except **Initialize SDK** need the SDK to be initialized first. If
> another tag can fire on the same event as the initialization tag, use **Tag Sequencing**
> (*Advanced Settings → Tag Sequencing → Fire a tag before*) to fire the initialization tag first.

## Actions

Each tag performs one action, chosen in the **Action** field.

| Action | What it does | Needs SDK 2.3.3+ |
|---|---|---|
| Initialize SDK | Loads the SDK if needed and initializes it | — |
| Track Event | Tracks a custom or revenue event | ✓ |
| Track Screen | Tracks a screen view manually | ✓ |
| Start Tracking | Starts or resumes tracking | — |
| Stop Tracking | Pauses tracking | — |
| Set Push Notification Token (FCM) | Sends the browser's FCM token | ✓ |
| Set Log Level | Changes the console log level for the current page view | ✓ |
| Enable / Disable SDK | Turns sending data on or off | ✓ |
| Flush | Clears queued and stored SDK data and stops tracking | — |

The SDK version only matters when your site loads the SDK itself. When the template loads the
SDK, it always gets the latest version.

### Initialize SDK

Initialize once per page load, on the **Initialization - All Pages** trigger.

**Required settings**

| Field | Description |
|---|---|
| App Token | Your app's token from the WiseTrack panel. |
| Client Secret | Your app's client secret from the WiseTrack panel. Required to authenticate SDK requests. |
| App Version | The version of your web app, for example `1.0.0`. Used to segment data by release. |
| App Framework | The framework your site is built with: Native (plain JavaScript / HTML), React, Vue.js, Angular, WordPress, Flutter, React Native, Unity or Unreal Engine. Default: Native. |

**Screen Tracking**

| Field | Default | Description |
|---|---|---|
| Automatically track screen views | On | Tracks a screen view on page load and on every URL change (`pushState`, `replaceState`, `popstate`, `hashchange`). Single-page apps are covered without extra tags. |
| Automatically track dialogs as dialog screens | On | Tracks each native `<dialog>` element opened with `show()` or `showModal()` as a screen of type `dialog`. To skip a dialog, add the `data-wt-no-track` attribute to it. |
| Excluded Screen Paths | — | Paths that are not tracked automatically. Each entry must start with `/`. An entry excludes the exact path and every path below it: `/admin` excludes `/admin` and `/admin/users`, but not `/administrator`. |
| Excluded Screen Patterns | — | Regular expressions in JavaScript syntax, written without the surrounding slashes. Paths that match are not tracked automatically. For example, `^/user/\d+/settings$` excludes `/user/42/settings`. Matching is case-sensitive. Invalid patterns are ignored and logged in the console. |
| Sensitive Query Parameters | — | Query parameters to remove from the screen URL and screen parameters before sending. These are added to the built-in list: `token`, `password`, `api_key`, `session_id`, `email`. Keys are case-sensitive. |
| Deduplication Window (ms) | `100` | Repeated screen views of the same URL within this window count once. This prevents duplicates when a router updates the URL several times for one navigation. |

Excluded paths, excluded patterns, sensitive parameters and the deduplication window only apply
when automatic screen tracking is on.

**Advanced Settings**

| Field | Default | Description |
|---|---|---|
| Environment | Production | Use **Sandbox** while testing so test data stays separate from live data. |
| Log Level | Warning | Minimum level of SDK messages in the browser console: Debug, Info, Warning, Error or None. |
| Start tracking automatically after initialization | On | Turn this off to wait before tracking starts, for example until the user gives consent. Then fire a **Start Tracking** tag. |
| Tracking Waiting Time (seconds) | `0` | Delay between initialization and the automatic start of tracking. Decimals are allowed. |
| Enable deeplink handling | On | Lets the SDK track and handle WiseTrack deeplinks. |
| Push deeplinks to the dataLayer | Off | Pushes each deeplink to the `dataLayer`. See [Deeplinks in the dataLayer](#deeplinks-in-the-datalayer). |
| Deeplink Event Name | `wisetrack_deeplink` | The `event` value of the deeplink `dataLayer` push. |
| Default Tracker | — | Optional. The name of a default tracker from the WiseTrack panel to attribute this traffic to. |
| Custom Device ID | — | Optional. Your own identifier for this browser, for example a first-party ID read from a cookie variable. If empty, the SDK generates one. |

### Track Event

| Field | Description |
|---|---|
| Event Name | The event name as defined in the WiseTrack panel, for example `purchase`. 1 to 50 characters. |
| Event Type | **Default** for regular user actions. **Revenue** for purchases, subscriptions and other monetary events. |
| Revenue Amount | Shown only for **Revenue**. A number that uses `.` as the decimal separator, for example `99.99`, or a variable. |
| Currency Code | Shown only for **Revenue**. A 3-letter uppercase ISO 4217 code, or a variable that returns one. Default: `IRR`. |
| Event Parameters | Optional key/value pairs. Keys can be up to 50 characters and values up to 100. Do not use quotes, backslashes, `%` or line breaks; the SDK rejects them. |

Supported currencies: IRR, USD, EUR, GBP, AED, IQD, SAR, OMR, TRY, JPY, AUD, CAD, CHF, CNY, SEK, NZD,
MXN, SGD, HKD, NOK, KRW, RUB, INR, BRL, ZAR, BTC, ETH, LTC.

Every field accepts GTM variables, so you can read values from the `dataLayer`. For example:

```text
Action:          Track Event
Event Name:      purchase
Event Type:      Revenue
Revenue Amount:  {{DLV - revenue_amount}}
Currency Code:   {{DLV - currency}}
Event Parameters:
  order_id       {{DLV - order_id}}
Trigger:         Custom Event – wt_purchase
```

### Track Screen

Use **Track Screen** for views that do not change the URL, such as tabs, checkout steps, bottom
sheets and custom overlays. Also use it for all screens when automatic screen tracking is off.

| Field | Description |
|---|---|
| Screen Name | A unique identifier for the screen, for example `checkout` or `product_details`. 1 to 150 characters. |
| Screen Display Name | Optional. A readable label shown in WiseTrack reports, for example `Checkout`. |
| Screen Type | Other, Page, Dialog, Bottom Sheet or WebView. Default: Other. |
| Screen Parameters | Optional key/value pairs, with the same limits as event parameters. |

### Start Tracking / Stop Tracking

**Stop Tracking** pauses tracking: nothing is sent until a **Start Tracking** tag fires. **Start
Tracking** starts or resumes it. Use them together with the *Start tracking automatically* option
to follow your consent banner. See [Consent-based tracking](#consent-based-tracking).

### Set Push Notification Token (FCM)

| Field | Description |
|---|---|
| FCM Token | The browser's Firebase Cloud Messaging registration token. It usually comes from a Data Layer Variable that your site pushes after calling `getToken()`. |

The SDK sends each token only once. If the SDK is not enabled yet, it retries a few times.

### Set Log Level

Changes the SDK's console log level for the rest of the current page view, for example to turn on
**Debug** logs only in Preview mode. On the next page load, the level from the initialization tag
applies again.

### Enable / Disable SDK

**Disable** keeps the SDK loaded but stops sending data. Events and screens tracked while the SDK
is disabled are held and sent when it is enabled again.

This action works only after the SDK has loaded its configuration from WiseTrack, and only when
the app is not disabled in the WiseTrack panel. Otherwise the tag fails. Fire it on a trigger that
comes after initialization has finished, not on the same trigger. On the next page load, the state
from the WiseTrack panel applies again.

### Flush

> **Warning:** Flush discards queued events, clears all data the SDK has stored in the browser, and
> stops tracking. Tracking does not resume on its own: fire a **Start Tracking** tag or reload the
> page.

Use it when a user logs out, or when a user asks for their data to be deleted.

## Deeplinks in the dataLayer

When **Push deeplinks to the dataLayer** is on, the template pushes every deeplink the SDK receives.
That includes the WiseTrack link that opened the page and any deferred deeplink resolved after
install. Each push looks like this:

```js
{
  event: 'wisetrack_deeplink',              // the "Deeplink Event Name" setting
  wisetrack_deeplink: 'https://…',          // the deeplink URL
  wisetrack_deeplink_deferred: false        // true for a deferred deeplink
}
```

To use it:

1. Create two **Data Layer Variables**, for `wisetrack_deeplink` and `wisetrack_deeplink_deferred`.
2. Create a **Custom Event** trigger with the event name `wisetrack_deeplink`.
3. Attach your tags to that trigger. For example, track a `deeplink_opened` event or send the user to the right page.

Each deeplink is pushed once per page load.

## Recipes

### Consent-based tracking

```text
Tag: WiseTrack – Init
  Action: Initialize SDK
  Start tracking automatically: off
  Trigger: Initialization - All Pages

Tag: WiseTrack – Start Tracking
  Action: Start Tracking
  Trigger: Custom Event – consent_granted

Tag: WiseTrack – Stop Tracking
  Action: Stop Tracking
  Trigger: Custom Event – consent_revoked
```

### E-commerce

Your site pushes this to the `dataLayer`:

```js
dataLayer.push({ event: 'wt_purchase', order_id: 'A-1001', revenue_amount: 149.99, currency: 'USD' });
```

Then create a **Track Event** tag with Event Type **Revenue**. Read the amount, currency and
`order_id` parameter from Data Layer Variables, and fire the tag on a *Custom Event* trigger for
`wt_purchase`.

### Single-page apps

You do not need anything extra: automatic screen tracking follows route changes. Add your admin or
internal routes to **Excluded Screen Paths**. Add views that keep the URL, such as a filter sheet or
a checkout step, with **Track Screen** tags fired on `dataLayer` events.

### Debugging in Preview mode only

Keep **Log Level** at *Warning* in the initialization tag. Then add a **Set Log Level** tag (Debug)
that fires on *Initialization - All Pages* only when the built-in `{{Debug Mode}}` variable equals
`true`. Use Tag Sequencing so it fires after the initialization tag.

### Logout

Fire a **Flush** tag on your logout event, for example *Custom Event – user_logout*.

## SDK loading and compatibility

- **When the site does not load the SDK:** the Initialize SDK tag loads the latest version from `https://unpkg.com/wisetrack/dist/cdn/sdk.bundle.min.js`. If unpkg fails, it loads it from `https://cdn.jsdelivr.net/npm/wisetrack/dist/cdn/sdk.bundle.min.js`.
- **When the site already loads the SDK** (as `window.WiseTrackSDK`): the template uses that copy and does not load another one.
- **When that copy is older than 2.3.3:**
  - Track Event, Track Screen, Set Push Notification Token, Set Log Level and Enable / Disable SDK fail. Each one logs a message asking you to update the SDK.
  - Initialize SDK still works, but it skips Excluded Screen Patterns and the deeplink `dataLayer` push, because the older SDK cannot use them.
  - Update the SDK on the site to use these features.
- **All tags except Initialize SDK** fail and log `SDK not loaded. Please initialize first.` if they fire before the SDK exists on the page.

## Permissions

The template asks for these permissions only:

| Permission | Why |
|---|---|
| Injects scripts | Loads the SDK bundle from `unpkg.com` and, as a fallback, from `cdn.jsdelivr.net`. |
| Accesses global variables | Reads `WiseTrackSDK` and calls the SDK methods the actions use, under `WiseTrackSDK.WiseTrack.instance.*`. Writes to `dataLayer` to push deeplinks. |
| Logs to console | Writes diagnostic messages, in Preview and debug mode only. |

## Troubleshooting

| Symptom | What to check |
|---|---|
| `WiseTrack SDK not found` or `SDK not loaded. Please initialize first.` | The tag fired before the SDK was initialized. Use Tag Sequencing, or a later trigger. Check that the CDN is not blocked by a content security policy or an ad blocker. |
| `… needs the latest WiseTrack SDK` | Your site loads an SDK older than 2.3.3. Update it, or remove it so that the template loads the latest version. |
| The Enable / Disable SDK tag fails | The SDK has not loaded its configuration yet, or the app is disabled in the WiseTrack panel. Fire the tag on a later trigger. |
| Events are missing in WiseTrack | Check the App Token, Client Secret and Environment. Check that the event name matches the WiseTrack panel, and that parameters do not contain quotes, backslashes or `%`. |
| A revenue event is rejected | The amount must be a number, such as `99.99` rather than `99,99`. The currency must be a supported uppercase code. |
| Screens are tracked twice | Increase **Deduplication Window (ms)**. Do not use a Track Screen tag for views that automatic tracking already records. |
| A page is not tracked | Check whether the page matches **Excluded Screen Paths** or **Excluded Screen Patterns**. For dialogs, check for a `data-wt-no-track` attribute. |

Set **Log Level** to **Debug** to see the SDK's own messages in the browser console.

## Support

- WiseTrack documentation: <https://docs.wisetrack.io>
- WiseTrack Web SDK: <https://github.com/wisetrack-io/web-sdk>
- Template issues and feature requests: [GitHub Issues](https://github.com/wisetrack-io/gtm-template/issues)

## License

This template is licensed under the [Apache License 2.0](LICENSE).

By using or modifying this template, you agree to the
[Google Tag Manager Community Template Gallery Developer Terms of Service](https://developers.google.com/tag-manager/gallery-tos).
