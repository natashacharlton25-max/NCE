# Emaillit Module

> **Purpose:** Email delivery infrastructure - API and SMTP transport
> **Type:** Integration Module
> **Status:** Planning

---

## Overview

Emaillit is the email delivery layer - it handles the actual sending of emails via API (SendGrid, Mailgun, etc.) or SMTP. It doesn't compose emails, just delivers them. Think of it as the postal service - it takes a sealed envelope and delivers it.

```
Composed email → Emaillit.send() → API/SMTP → Delivered
```

**Why separate from Email module:**
- Email module composes (creates the content)
- Emaillit delivers (sends via transport)
- Can swap transport providers without changing composition logic

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `send(email, options)` | Send single email | email, options | sendResult |
| `sendBatch(emails, options)` | Send multiple emails | emails, options | batchResult |
| `validateEmail(address)` | Validate email address | address | validationResult |
| `checkStatus(messageId)` | Check delivery status | messageId | status |
| `getProvider()` | Get current provider config | - | providerConfig |
| `setProvider(provider)` | Switch email provider | provider | switched |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `Emaillit.sendWithRetry` | Send with retry on failure | send → (retry if failed) |
| `Emaillit.sendBatchThrottled` | Send batch with rate limiting | sendBatch (throttled) |

---

## Detailed Function Specs

### send(email, options)

Sends a single email via configured provider.

```javascript
// Input
{
  email: {
    to: "client@example.com",
    from: "hello@nce.com",
    subject: "Your Brand Kit is Ready",
    html: "<html>...</html>",
    text: "Plain text version...",
    attachments: [
      { filename: "brand-guide.pdf", content: "base64...", type: "application/pdf" }
    ]
  },
  options: {
    provider: "sendgrid",  // or "smtp", "mailgun"
    priority: "normal",
    trackOpens: true,
    trackClicks: true
  }
}

// Process
1. Validate email structure
2. Get provider credentials
3. Send via provider API/SMTP
4. Return result with message ID

// Output (success)
{
  success: true,
  messageId: "msg_abc123",
  provider: "sendgrid",
  timestamp: "2026-01-17T14:30:52Z"
}

// Output (failure)
{
  success: false,
  error: {
    code: "INVALID_RECIPIENT",
    message: "Email address bounced"
  },
  retryable: false
}
```

### sendBatch(emails, options)

Sends multiple emails with batching.

```javascript
// Input
{
  emails: [
    { to: "client1@example.com", ... },
    { to: "client2@example.com", ... }
  ],
  options: {
    batchSize: 100,
    delayBetweenBatches: 1000,  // ms
    provider: "sendgrid"
  }
}

// Output
{
  success: true,
  sent: 50,
  failed: 2,
  results: [
    { to: "client1@example.com", messageId: "msg_1", status: "sent" },
    { to: "client2@example.com", messageId: null, status: "failed", error: "..." }
  ]
}
```

### checkStatus(messageId)

Checks delivery status of sent email.

```javascript
// Input
"msg_abc123"

// Output
{
  messageId: "msg_abc123",
  status: "delivered",  // queued, sent, delivered, bounced, opened, clicked
  events: [
    { event: "sent", timestamp: "2026-01-17T14:30:52Z" },
    { event: "delivered", timestamp: "2026-01-17T14:30:55Z" },
    { event: "opened", timestamp: "2026-01-17T15:12:30Z" }
  ]
}
```

---

## Provider Configuration

### SendGrid

```json
{
  "provider": "sendgrid",
  "apiKeyRef": "SENDGRID_API_KEY",
  "endpoint": "https://api.sendgrid.com/v3/mail/send",
  "rateLimit": 100,
  "features": ["tracking", "templates", "attachments"]
}
```

### SMTP

```json
{
  "provider": "smtp",
  "host": "smtp.example.com",
  "port": 587,
  "secure": true,
  "auth": {
    "userRef": "SMTP_USER",
    "passRef": "SMTP_PASS"
  }
}
```

### Mailgun

```json
{
  "provider": "mailgun",
  "apiKeyRef": "MAILGUN_API_KEY",
  "domain": "mail.nce.com",
  "endpoint": "https://api.mailgun.net/v3"
}
```

---

## Config

### module.config.json

```json
{
  "module": "Emaillit",
  "version": "1.0.0",
  "description": "Email delivery infrastructure",
  "type": "integration",

  "functions": [
    {
      "name": "send",
      "description": "Send single email",
      "input": ["email", "options"],
      "output": "sendResult"
    },
    {
      "name": "sendBatch",
      "description": "Send multiple emails",
      "input": ["emails", "options"],
      "output": "batchResult"
    },
    {
      "name": "validateEmail",
      "description": "Validate email address",
      "input": ["address"],
      "output": "validationResult"
    },
    {
      "name": "checkStatus",
      "description": "Check delivery status",
      "input": ["messageId"],
      "output": "status"
    },
    {
      "name": "getProvider",
      "description": "Get current provider config",
      "input": [],
      "output": "providerConfig"
    },
    {
      "name": "setProvider",
      "description": "Switch email provider",
      "input": ["provider"],
      "output": "switched"
    }
  ],

  "steps": [
    {
      "name": "sendWithRetry",
      "description": "Send with retry on failure",
      "sequence": ["send"]
    },
    {
      "name": "sendBatchThrottled",
      "description": "Send batch with rate limiting",
      "sequence": ["sendBatch"]
    }
  ],

  "config": {
    "defaultProvider": "sendgrid",
    "maxRetries": 3,
    "batchSize": 100,
    "rateLimitPerSecond": 10
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| APIKeyManager | Getting provider credentials |
| Fetchers | Making HTTP requests |

---

## Used By

| Module | How |
|--------|-----|
| Email | Delivers composed emails |
| Notification chains | Sending alerts |

---

*Last updated: 2026-01-17*
