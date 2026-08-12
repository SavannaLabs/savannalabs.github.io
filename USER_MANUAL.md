# Interceptor — User Manual

**Version:** 1.0  
**Platform:** Safari (macOS 12.3+)

Interceptor is a Safari Web Extension that intercepts and overrides `fetch()` and `XMLHttpRequest` (XHR) responses in real time. It allows you to mock API responses, modify live data, and test edge cases without touching your backend.

---

## Table of Contents

1. [Installation](#installation)
2. [Quick Start](#quick-start)
3. [The Popup](#the-popup)
4. [Managing Rules](#managing-rules)
5. [Rule Editor](#rule-editor)
6. [Mock Response](#mock-response)
7. [Modify Response](#modify-response)
8. [Transforms](#transforms)
9. [Per-Site Toggle](#per-site-toggle)
10. [Export, Import & Share](#export-import--share)
11. [How Interception Works](#how-interception-works)
12. [Limitations](#limitations)
13. [Troubleshooting](#troubleshooting)

---

## Installation

1. Open **Xcode** and build the `Interceptor` project.
2. Open **Safari** and go to **Safari > Settings > Extensions**.
3. Enable the **Interceptor** extension.
4. The Interceptor icon appears in the Safari toolbar.

> **Note:** Safari caches extension bundles aggressively. After code changes, toggle the extension off and on in Safari Settings, or delete the app's DerivedData folder in Xcode.

---

## Quick Start

1. Click the **Interceptor** icon in the Safari toolbar to open the popup.
2. The example rule is pre-loaded — navigate to `jsonplaceholder.typicode.com` and make a fetch request.
3. The response is intercepted and replaced with the mock data.
4. Click **+ Add Rule** to create your own rules.

---

## The Popup

The popup is the main control panel. It has four areas:

### Header

Shows the extension icon, name, and version badge.

### Site Status Bar

Displays the hostname of the current tab and a toggle button to enable or disable interception for that specific site (see [Per-Site Toggle](#per-site-toggle)).

### Toolbar

| Button | Description |
|--------|-------------|
| **Select All** | Select all rule checkboxes for bulk operations. |
| **None** | Deselect all rule checkboxes. |
| **Export** | Export selected rules as a JSON file. Shows the count, e.g. `Export (3)`. Disabled when nothing is selected. |
| **Share** | Share selected rules via the macOS share sheet (AirDrop, Messages, Mail, etc.). Disabled when nothing is selected. |
| **Import** | Import rules from a `.json` file. |

### Rules List

Each rule is displayed as a card with:

- **Checkbox** — Select the rule for export/share.
- **Toggle switch** — Enable or disable the rule (green = on, gray = off).
- **Rule name** — The human-readable name.
- **Method badge** — `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, or `ANY`.
- **Action badge** — `mock` (blue) or `modify` (purple).
- **URL pattern** — The matching pattern in monospace.
- **Edit button** — Opens the rule editor.
- **Delete button** — Permanently removes the rule.

> **Note:** Deleting asks for confirmation inline — the button turns red and reads **Confirm?**. Click it again within 6 seconds to delete, or click anywhere else to cancel.

### Add Rule

Click **+ Add Rule** at the bottom of the list to open the rule editor in "Add" mode.

---

## Managing Rules

### Creating a Rule

1. Click **+ Add Rule**.
2. Fill in the **Rule** section (name, URL pattern, method, action type).
3. Fill in the **Mock Response** or **Modify Response** section.
4. Click **Save**.

### Editing a Rule

1. Click the **Edit** button (pencil icon) on any rule card.
2. Modify the fields as needed.
3. Click **Save**.

### Enabling / Disabling a Rule

Toggle the switch on the rule card. Green means enabled; gray means disabled. Disabled rules are skipped during interception.

### Selecting Rules for Export/Share

Check the checkbox on each rule you want to export or share, then click **Export** or **Share** in the toolbar. Use **Select All** / **None** for bulk selection.

---

## Rule Editor

The editor opens in a separate window (520 × 700 px) with an accordion layout.

### Section 1: Rule (always visible)

| Field | Required | Description |
|-------|----------|-------------|
| **Name** | Yes | A human-readable label for the rule. |
| **URL Pattern** | Yes | A glob pattern using `*` as wildcard. Example: `*://api.example.com/*`. |
| **Method** | No | `Any` (matches all methods), `GET`, `POST`, `PUT`, `PATCH`, or `DELETE`. |
| **Action Type** | Yes | `Mock Response` or `Modify Response`. |

### Validation

- Name and URL Pattern cannot be empty.
- Headers (if provided) must be a valid JSON object with valid HTTP header names.
- Transforms are validated for length (max 1000 chars per pattern).

Errors are displayed in a red banner at the bottom of the form.

---

## Mock Response

Returns a completely fabricated response without making any network request.

| Field | Default | Description |
|-------|---------|-------------|
| **Status Code** | `200` | The HTTP status code to return. |
| **Response Body** | empty | The full response body (e.g. JSON string). |
| **Headers** | empty | A JSON object of response headers (e.g. `{"Content-Type": "application/json"}`). |
| **Delay (ms)** | `0` | Milliseconds to wait before returning the response. Simulates network latency. |

### Example

To mock a JSON API endpoint:

- **URL Pattern:** `*://api.example.com/users`
- **Method:** `GET`
- **Status Code:** `200`
- **Response Body:** `[{"id": 1, "name": "John Doe"}]`
- **Headers:** `{"Content-Type": "application/json"}`

---

## Modify Response

Intercepts the real response and applies transformations to it. The actual network request is made, but the response is modified before it reaches the page.

| Field | Description |
|-------|-------------|
| **Override Status Code** | Optional. If set, replaces the original status code. Leave empty to pass through. |
| **Delay (ms)** | Milliseconds to wait before the request is made. Simulates a slow endpoint. |
| **Response Transforms** | A list of text transforms applied sequentially to the response body. |

> **Note:** Modify actions only work with `fetch()`. XHR requests fall through to the original response.

---

## Transforms

Transforms are applied sequentially in the order they are listed. Click **+ Add Transform** to add one, or **✕** to remove it.

### Transform Types

| Type | Pattern Field | Replacement Field | Behavior |
|------|---------------|-------------------|----------|
| **Replace** | Regex pattern | Replacement string | Replaces all matches of the regex in the response body. Uses the `g` (global) flag by default. |
| **Regex Replace** | Regex pattern | Replacement string | Same as Replace. Both use JavaScript `RegExp` with the global flag. |
| **JSON Set** | Dot-separated path | Value | Parses the body as JSON, navigates to the specified path (e.g. `data.items.0.name`), and sets the value. Creates intermediate objects if missing. |
| **Prepend** | Text to prepend | *(unused)* | Prepends the text to the beginning of the response body. |
| **Append** | Text to append | *(unused)* | Appends the text to the end of the response body. |

### Examples

**Replace all `"active": false` with `"active": true`:**
- Type: `Replace`
- Pattern: `"active":\s*false`
- Replacement: `"active": true`

**Set a nested JSON value:**
- Type: `JSON Set`
- Pattern: `data.user.name`
- Replacement: `Jane Doe`

**Add a footer to HTML responses:**
- Type: `Append`
- Pattern: `<footer>Modified by Interceptor</footer>`

---

## Per-Site Toggle

The site toggle in the popup controls whether Interceptor is active for the current website.

| Button Label | State | Behavior |
|--------------|-------|----------|
| **De-authorize** (red) | Interception is ON | Click to turn interception OFF for this site. |
| **Authorize** (green) | Interception is OFF | Click to turn interception back ON for this site. |

Disabled sites are stored locally and persist across sessions.

---

## Export, Import & Share

### Export

1. Select one or more rules using the checkboxes.
2. Click **Export** in the toolbar.
3. A file named `interceptor-rules.json` is downloaded.
4. The export window closes automatically.

The exported file contains a JSON envelope:

```json
{
  "name": "Interceptor Rules",
  "version": 1,
  "exportedAt": "2026-07-29T12:00:00.000Z",
  "rules": [ ... ]
}
```

### Share

1. Select one or more rules using the checkboxes.
2. Click **Share** in the toolbar.
3. The native macOS share sheet opens.
4. Choose AirDrop, Messages, Mail, Notes, or any other share destination.

If the share sheet is unavailable, the rules are copied to the clipboard as a fallback.

### Import

1. Click **Import** in the toolbar.
2. Select a `.json` file (either a bare array of rules or the export envelope format).
3. Rules are validated and imported with new unique IDs.
4. Invalid rules are skipped with a notification.

Imported rules are enabled by default.

---

## How Interception Works

Interceptor uses a multi-layer architecture:

1. **Content script** (`content.js`) runs on every page and bridges between `browser.storage` and the page's JavaScript context.
2. **Injected script** runs in the page's main world, overriding `window.fetch` and `XMLHttpRequest`.
3. When a request matches a rule's URL pattern and method:
   - **Mock:** Returns a fabricated response immediately (or after the configured delay).
   - **Modify:** Waits for the configured delay, makes the real request, then applies transforms to the response body before returning it.
4. Rules update in real time — changes made in the popup take effect immediately without reloading the page.

### Pattern Matching

URL patterns use `*` as a wildcard:

| Pattern | Matches |
|---------|---------|
| `*://api.example.com/*` | Any protocol, any path on `api.example.com` |
| `*://*.example.com/api/*` | Any subdomain of `example.com`, `/api/` paths |
| `https://api.example.com/users/*` | Only HTTPS, `/users/` paths |

Relative URLs (starting with `/`) are resolved against the current page's origin.

### First-Match Wins

If multiple rules match a request, the first enabled match is applied. There is no priority system.

---

## Limitations

- **XHR Modify:** The "Modify Response" action type only works with `fetch()`. XHR requests are only affected by "Mock Response" rules.
- **No body interception:** Only response bodies can be modified. Request bodies cannot be intercepted or transformed.
- **Single edit window:** Only one rule editor window can be open at a time.
- **No undo:** Deleting asks for confirmation, but once confirmed the rule cannot be recovered. Export rules you care about.
- **No keyboard shortcuts:** All actions are performed through the popup UI.

---

## Troubleshooting

### Rules don't seem to work

1. Check that the rule is **enabled** (toggle switch is green).
2. Check that the site is **not de-authorized** (the site toggle should say "De-authorize" in red).
3. Verify the URL pattern matches the request URL exactly (wildcards must align with the path structure).
4. Check the method matches (or is set to "Any").
5. Try disabling and re-enabling the extension in Safari Settings.

### Changes don't take effect

Rules update in real time. If changes aren't reflected:
1. Reload the page.
2. Toggle the extension off and on in Safari Settings.

### Export doesn't download

Safari may block popups. Allow popups for the current site and try again. As a fallback, use the **Share** button.

### Import says rules are invalid

Ensure the JSON file contains either:
- A bare array of rule objects, or
- An object with a `"rules"` property containing an array.

Each rule must have `name`, `urlPattern`, and `action` with a valid `type` (`"mock"` or `"modify"`).

### Dark mode

Interceptor fully supports macOS dark mode. The UI adapts automatically based on your system appearance.

---

## Data Format Reference

### Rule Object

```json
{
  "id": "rule_1722268800000_a1b2",
  "name": "My Rule",
  "urlPattern": "*://api.example.com/*",
  "method": "GET",
  "enabled": true,
  "action": {
    "type": "mock",
    "statusCode": 200,
    "responseBody": "{\"key\": \"value\"}",
    "headers": {"Content-Type": "application/json"},
    "delay": 0
  }
}
```

### Modify Action

```json
{
  "type": "modify",
  "overrideStatus": 200,
  "transforms": [
    {"type": "replace", "pattern": "foo", "replacement": "bar"},
    {"type": "json_set", "pattern": "data.count", "replacement": "42"}
  ]
}
```

### Export Envelope

```json
{
  "name": "Interceptor Rules",
  "version": 1,
  "exportedAt": "2026-07-29T12:00:00.000Z",
  "rules": []
}
```
