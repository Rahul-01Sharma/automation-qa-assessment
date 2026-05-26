# Bonus: GoCart Uptime Monitor — n8n Workflow

## Overview
This n8n workflow pings the GoCart live deployment every **5 minutes**, checks the HTTP status code, and sends a **Discord alert** if the site is down. A **daily summary** is also posted every 24 hours.

---

## Workflow Structure

```
[Every 5 Min Trigger]
        ↓
  [Ping GoCart]          ← continueOnFail: true (won't crash on timeout)
        ↓
[Check Status & Build Payload]   ← Code node: extracts statusCode, builds message
        ↓
   [Is Site Down?]       ← IF node: statusCode !== 200
      ↙       ↘
[Retry Note] [Send Discord Alert]     [Log OK (no alert)]

[Daily Summary Trigger] → [Send Daily Summary to Discord]
```

---

## Features

| Feature | Detail |
|---|---|
| **Trigger** | Schedule — every 5 minutes |
| **Target URL** | https://gocart-gs.vercel.app |
| **Down detection** | HTTP status ≠ 200 OR no response (timeout) |
| **Alert channel** | Discord webhook |
| **Retry logic** | `continueOnFail: true` on HTTP node prevents silent crash; Retry Note node logs the event before alerting |
| **Daily summary** | Second schedule trigger fires every 24h and posts a summary to Discord |
| **Error handling** | `continueOnFail` on all HTTP nodes; Error Handler node for unexpected failures |

---

## How the Error Path Works

- The **Ping GoCart** node has `continueOnFail: true` — if the site times out or DNS fails, execution continues rather than stopping silently.
- The **Check Status & Build Payload** Code node reads `statusCode` from the response. If the ping failed entirely, `statusCode` defaults to `0`, which triggers the "down" branch.
- The **Is Site Down?** IF node routes to the alert path for any status other than `200`.
- Discord alerts include the status code, URL, and timestamp so the on-call person has immediate context.

---

## Setup Instructions

1. Import `Bonus_UptimeMonitor.json` into your n8n instance via **Workflows → Import**.
2. Create a Discord webhook in your server: **Server Settings → Integrations → Webhooks → New Webhook → Copy URL**.
3. In n8n, add a **Credential** of type `Header Auth` (or plain string) and paste the webhook URL.
4. Update the two **Send Discord** nodes to reference your credential.
5. Activate the workflow — it will begin pinging every 5 minutes automatically.

---

## Credentials
- **Discord Webhook URL** — stored in n8n Credentials store, never hardcoded in the workflow.
