# Webhook integration

## Overview

Webhooks enable you to receive real-time notifications when customer verification statuses change. Instead of polling the API repeatedly, your application receives HTTP POST requests with event data as soon as changes occur. This approach provides near-instant updates with minimal overhead, making it the recommended method for monitoring verification progress.

Webhooks deliver notifications within 5 seconds of status changes and significantly reduce API calls compared to polling. They enable responsive user experiences and scale efficiently with large customer volumes.

**Related guides:**

* For verification states see [Customer states](../concepts/customer-states.md)
* For verification workflow see [Verification workflow](../concepts/verification-workflow.md)
* For API reference see [<!-- PHASE6_API_REFERENCE: Customer API -->](../../../api-reference/customers.md)

## Benefits of webhooks

### Comparison with polling

| Approach | Latency | Efficiency | Complexity |
|----------|---------|------------|------------|
| **Webhooks** | Real-time (< 5s) | High - event-driven | Medium - requires endpoint setup |
| **Polling** | Delayed (30s-5m) | Low - repeated API calls | Low - simple GET requests |

Webhooks provide these advantages:

* Near-instant notifications (< 5 seconds)
* Reduced API calls and rate limit consumption
* Efficient scaling with large customer volumes
* Responsive user experiences

## How webhooks work

```
┌─────────────────┐
│ Verification    │
│ Status Changes  │
└────────┬────────┘
         │
         │ Event occurs
         ▼
┌─────────────────┐
│ API Server      │
│ Processes event │
└────────┬────────┘
         │
         │ Sends webhook
         ▼
┌─────────────────┐
│ Your Webhook    │
│ Endpoint        │ ◄── You build this
└────────┬────────┘
         │
         │ Process event
         ▼
┌─────────────────┐
│ Your Application│
│ Updates UI      │
└─────────────────┘
```

### Event flow

1. **Customer verification status changes** (e.g., CREATED → UNDER_REVIEW)
2. **API generates webhook event** with status change details
3. **API sends HTTP POST** to your configured webhook URL
4. **Your endpoint receives event** and returns 200 OK
5. **Your application processes event** asynchronously

## Setting up webhooks

{% stepper %}
{% step %}
### Create webhook endpoint

Create an HTTPS endpoint in your application to receive webhook events.

{% tabs %}
{% tab title="JavaScript (Express)" %}
```javascript
const express = require('express');
const crypto = require('crypto');

const app = express();
app.use(express.json());

app.post('/webhooks/verification', async (req, res) => {
  const signature = req.headers['x-webhook-signature'];
  const timestamp = req.headers['x-webhook-timestamp'];

  // Verify signature (see security section)
  if (!verifyWebhookSignature(req.body, signature, timestamp)) {
    return res.status(401).json({ error: 'Invalid signature' });
  }

  // Return 200 immediately
  res.status(200).json({ received: true });

  // Process event asynchronously
  processWebhookEvent(req.body).catch(console.error);
});

app.listen(3000);
```
{% endtab %}

{% tab title="Python (Flask)" %}
```python
from flask import Flask, request, jsonify
import hmac
import hashlib
import json

app = Flask(__name__)

@app.route('/webhooks/verification', methods=['POST'])
def webhook_handler():
    signature = request.headers.get('X-Webhook-Signature')
    timestamp = request.headers.get('X-Webhook-Timestamp')

    # Verify signature
    if not verify_webhook_signature(request.data, signature, timestamp):
        return jsonify({'error': 'Invalid signature'}), 401

    # Return 200 immediately
    response = jsonify({'received': True})

    # Process event asynchronously
    event_data = request.get_json()
    process_webhook_event(event_data)

    return response, 200

if __name__ == '__main__':
    app.run(port=3000)
```
{% endtab %}

{% tab title="Go" %}
```go
package main

import (
    "crypto/hmac"
    "crypto/sha256"
    "encoding/hex"
    "encoding/json"
    "net/http"
)

func webhookHandler(w http.ResponseWriter, r *http.Request) {
    signature := r.Header.Get("X-Webhook-Signature")
    timestamp := r.Header.Get("X-Webhook-Timestamp")

    var event map[string]interface{}
    if err := json.NewDecoder(r.Body).Decode(&event); err != nil {
        http.Error(w, "Invalid request", http.StatusBadRequest)
        return
    }

    // Verify signature
    if !verifyWebhookSignature(event, signature, timestamp) {
        http.Error(w, "Invalid signature", http.StatusUnauthorized)
        return
    }

    // Return 200 immediately
    w.WriteHeader(http.StatusOK)
    json.NewEncoder(w).Encode(map[string]bool{"received": true})

    // Process event asynchronously
    go processWebhookEvent(event)
}

func main() {
    http.HandleFunc("/webhooks/verification", webhookHandler)
    http.ListenAndServe(":3000", nil)
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Your endpoint must return a 200 status code within 10 seconds to acknowledge receipt. Process events asynchronously to avoid timeouts.
{% endhint %}
{% endstep %}

{% step %}
### Configure webhook URL

Contact your account manager to configure your webhook URL and obtain your webhook secret.

**Required information:**
- **Webhook URL**: Your HTTPS endpoint (e.g., `https://api.yourcompany.com/webhooks/verification`)
- **Webhook Secret**: A secure secret key for signature verification (provided by your account manager)

{% hint style="warning" %}
Webhook URLs must use HTTPS. HTTP endpoints are rejected for security reasons.
{% endhint %}
{% endstep %}

{% step %}
### Test your integration

Use sandbox mode to test webhook delivery:

1. Create a test customer
2. Trigger verification status changes
3. Verify your endpoint receives webhooks
4. Confirm signature verification works
5. Test error handling and retries

<!-- PHASE6_API_REFERENCE: Sandbox testing endpoints -->
{% endstep %}
{% endstepper %}

## Webhook events

### Event types

Your webhook endpoint receives these event types:

| Event Type | Description | Trigger |
|------------|-------------|---------|
| `customer.created` | New customer profile created | <!-- PHASE6_API_REFERENCE: POST /customers --> |
| `customer.updated` | Customer data modified | <!-- PHASE6_API_REFERENCE: PATCH /customers/:id --> |
| `customer.status_changed` | Verification status changed | Status transition |
| `verification.completed` | Verification finalized | Status → ACTIVE, REJECTED, or DEACTIVATED |
| `verification.rfi_required` | Additional information needed | Status → NEEDS_ADDITIONAL_INFORMATION |

### Event payload structure

All webhook events follow this structure:

```json
{
  "type": "customer.status_changed",
  "payload": {
    "customerId": "cus_1a2b3c4d5e6f",
    "previousStatus": "UNDER_REVIEW",
    "newStatus": "ACTIVE",
    "customerType": "INDIVIDUAL",
    "email": "user@example.com",
    "updatedAt": "2025-11-05T10:30:00Z"
  },
  "metadata": {
    "eventId": "evt_abc123xyz789",
    "occurredAt": "2025-11-05T10:30:00Z",
    "organizationId": "org_xyz789"
  }
}
```

### Event field descriptions

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Event type identifier |
| `payload` | object | Event-specific data |
| `payload.customerId` | string | Customer ID |
| `payload.previousStatus` | string | Previous customer status |
| `payload.newStatus` | string | New customer status |
| `metadata.eventId` | string | Unique event identifier (for idempotency) |
| `metadata.occurredAt` | timestamp | When event occurred |

## Webhook event examples

### Customer created

```json
{
  "type": "customer.created",
  "payload": {
    "customerId": "cus_1a2b3c4d5e6f",
    "customerType": "INDIVIDUAL",
    "status": "CREATED",
    "email": "jane.smith@example.com",
    "firstName": "Jane",
    "lastName": "Smith",
    "createdAt": "2025-11-05T10:00:00Z"
  },
  "metadata": {
    "eventId": "evt_create_123",
    "occurredAt": "2025-11-05T10:00:00Z",
    "organizationId": "org_xyz789"
  }
}
```

### Status changed to under review

```json
{
  "type": "customer.status_changed",
  "payload": {
    "customerId": "cus_1a2b3c4d5e6f",
    "previousStatus": "CREATED",
    "newStatus": "UNDER_REVIEW",
    "customerType": "INDIVIDUAL",
    "email": "jane.smith@example.com",
    "updatedAt": "2025-11-05T10:15:00Z"
  },
  "metadata": {
    "eventId": "evt_status_456",
    "occurredAt": "2025-11-05T10:15:00Z",
    "organizationId": "org_xyz789"
  }
}
```

### Verification completed (active)

```json
{
  "type": "verification.completed",
  "payload": {
    "customerId": "cus_1a2b3c4d5e6f",
    "previousStatus": "UNDER_REVIEW",
    "newStatus": "ACTIVE",
    "customerType": "INDIVIDUAL",
    "email": "jane.smith@example.com",
    "completedAt": "2025-11-05T12:30:00Z"
  },
  "metadata": {
    "eventId": "evt_complete_789",
    "occurredAt": "2025-11-05T12:30:00Z",
    "organizationId": "org_xyz789"
  }
}
```

### RFI required

```json
{
  "type": "verification.rfi_required",
  "payload": {
    "customerId": "cus_1a2b3c4d5e6f",
    "previousStatus": "UNDER_REVIEW",
    "newStatus": "NEEDS_ADDITIONAL_INFORMATION",
    "customerType": "INDIVIDUAL",
    "email": "jane.smith@example.com",
    "reason": "Additional document verification required",
    "updatedAt": "2025-11-05T11:00:00Z"
  },
  "metadata": {
    "eventId": "evt_rfi_101",
    "occurredAt": "2025-11-05T11:00:00Z",
    "organizationId": "org_xyz789"
  }
}
```

## Security and signature verification

### Why verify signatures

Webhook signature verification ensures:
* **Authenticity**: Request came from the API, not an attacker
* **Integrity**: Payload was not tampered with
* **Replay protection**: Prevents replay attacks

{% hint style="danger" %}
Always verify signatures. Never process webhook events without signature verification. This is critical for security.
{% endhint %}

### Signature headers

Every webhook request includes:

```http
X-Webhook-Signature: a1b2c3d4e5f6g7h8i9j0...
X-Webhook-Timestamp: 1699104000
```

| Header | Description |
|--------|-------------|
| `X-Webhook-Signature` | HMAC-SHA256 signature of payload |
| `X-Webhook-Timestamp` | Unix timestamp when webhook was sent |

### Signature verification process

**Signature formula:**
```
signature = HMAC-SHA256(webhook_secret, timestamp + "." + json_payload)
```

**Verification steps:**
1. Extract `timestamp` and `signature` from headers
2. Reconstruct signed content: `timestamp + "." + request_body`
3. Compute HMAC-SHA256 using your webhook secret
4. Compare computed signature with received signature
5. Verify timestamp is within 5 minutes of current time

### Implementation examples

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const crypto = require('crypto');

function verifyWebhookSignature(payload, signature, timestamp, secret) {
  // Check timestamp (prevent replay attacks)
  const currentTime = Math.floor(Date.now() / 1000);
  const timeDiff = Math.abs(currentTime - parseInt(timestamp));

  if (timeDiff > 300) { // 5 minutes
    console.error('Webhook timestamp too old');
    return false;
  }

  // Compute expected signature
  const payloadString = typeof payload === 'string'
    ? payload
    : JSON.stringify(payload);

  const signedContent = `${timestamp}.${payloadString}`;

  const expectedSignature = crypto
    .createHmac('sha256', secret)
    .update(signedContent)
    .digest('hex');

  // Constant-time comparison to prevent timing attacks
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expectedSignature)
  );
}

// Usage in webhook handler
app.post('/webhooks/verification', (req, res) => {
  const signature = req.headers['x-webhook-signature'];
  const timestamp = req.headers['x-webhook-timestamp'];
  const secret = process.env.WEBHOOK_SECRET;

  if (!verifyWebhookSignature(req.body, signature, timestamp, secret)) {
    return res.status(401).json({ error: 'Invalid signature' });
  }

  // Process webhook...
  res.status(200).json({ received: true });
});
```
{% endtab %}

{% tab title="Python" %}
```python
import hmac
import hashlib
import json
import time

def verify_webhook_signature(payload, signature, timestamp, secret):
    # Check timestamp (prevent replay attacks)
    current_time = int(time.time())
    time_diff = abs(current_time - int(timestamp))

    if time_diff > 300:  # 5 minutes
        print('Webhook timestamp too old')
        return False

    # Compute expected signature
    if isinstance(payload, dict):
        payload_string = json.dumps(payload, separators=(',', ':'))
    else:
        payload_string = payload

    signed_content = f"{timestamp}.{payload_string}"

    expected_signature = hmac.new(
        secret.encode('utf-8'),
        signed_content.encode('utf-8'),
        hashlib.sha256
    ).hexdigest()

    # Constant-time comparison
    return hmac.compare_digest(signature, expected_signature)

# Usage in webhook handler
@app.route('/webhooks/verification', methods=['POST'])
def webhook_handler():
    signature = request.headers.get('X-Webhook-Signature')
    timestamp = request.headers.get('X-Webhook-Timestamp')
    secret = os.environ['WEBHOOK_SECRET']

    if not verify_webhook_signature(request.data, signature, timestamp, secret):
        return jsonify({'error': 'Invalid signature'}), 401

    # Process webhook...
    return jsonify({'received': True}), 200
```
{% endtab %}

{% tab title="Go" %}
```go
package main

import (
    "crypto/hmac"
    "crypto/sha256"
    "encoding/hex"
    "encoding/json"
    "fmt"
    "math"
    "strconv"
    "time"
)

func verifyWebhookSignature(payload []byte, signature, timestamp, secret string) bool {
    // Check timestamp (prevent replay attacks)
    ts, err := strconv.ParseInt(timestamp, 10, 64)
    if err != nil {
        return false
    }

    currentTime := time.Now().Unix()
    timeDiff := math.Abs(float64(currentTime - ts))

    if timeDiff > 300 { // 5 minutes
        fmt.Println("Webhook timestamp too old")
        return false
    }

    // Compute expected signature
    signedContent := fmt.Sprintf("%s.%s", timestamp, string(payload))

    h := hmac.New(sha256.New, []byte(secret))
    h.Write([]byte(signedContent))
    expectedSignature := hex.EncodeToString(h.Sum(nil))

    // Constant-time comparison
    return hmac.Equal([]byte(signature), []byte(expectedSignature))
}

// Usage in webhook handler
func webhookHandler(w http.ResponseWriter, r *http.Request) {
    signature := r.Header.Get("X-Webhook-Signature")
    timestamp := r.Header.Get("X-Webhook-Timestamp")
    secret := os.Getenv("WEBHOOK_SECRET")

    body, _ := ioutil.ReadAll(r.Body)

    if !verifyWebhookSignature(body, signature, timestamp, secret) {
        http.Error(w, "Invalid signature", http.StatusUnauthorized)
        return
    }

    // Process webhook...
    w.WriteHeader(http.StatusOK)
    json.NewEncoder(w).Encode(map[string]bool{"received": true})
}
```
{% endtab %}
{% endtabs %}

## Webhook delivery and retries

### Delivery guarantees

**Success criteria:** Your endpoint must return:
- HTTP status code `200-299`
- Within 10 seconds

**Retry policy:**
- **Attempts**: Up to 3 retries
- **Backoff**: Exponential (1s, 2s, 4s)
- **Timeout**: 10 seconds per attempt
- **Total retry window**: Approximately 15 seconds

### Retry behavior

```
Attempt 1 (immediate)
    ↓ Failed
Wait 1 second
    ↓
Attempt 2
    ↓ Failed
Wait 2 seconds
    ↓
Attempt 3 (final)
    ↓ Failed
    ↓
Event marked as failed
```

### Failure handling

If all retry attempts fail:
- Event is logged for debugging
- Alert sent to your account manager
- You can request manual redelivery via support

{% hint style="warning" %}
Always return 200 OK immediately, then process events asynchronously. Long processing times may cause timeouts and retries.
{% endhint %}

## Processing webhook events

### Recommended processing pattern

```javascript
// Anti-pattern: Synchronous processing
app.post('/webhooks', async (req, res) => {
  await updateDatabase(req.body);        // Slow
  await sendEmailNotification(req.body); // Slow
  res.status(200).json({ received: true });
});

// Best practice: Async processing
app.post('/webhooks', async (req, res) => {
  // Return 200 immediately
  res.status(200).json({ received: true });

  // Process asynchronously
  processWebhookAsync(req.body).catch(handleError);
});

async function processWebhookAsync(event) {
  await updateDatabase(event);
  await sendEmailNotification(event);
  await updateUICache(event);
}
```

### Idempotency

Handle duplicate webhook deliveries gracefully using `eventId`:

```javascript
async function processWebhookEvent(event) {
  const { eventId } = event.metadata;

  // Check if already processed
  const existingEvent = await db.webhookEvents.findOne({ eventId });

  if (existingEvent) {
    console.log(`Event ${eventId} already processed`);
    return; // Idempotent - skip processing
  }

  // Process event
  await handleStatusChange(event.payload);

  // Record as processed
  await db.webhookEvents.create({
    eventId,
    processedAt: new Date(),
    payload: event
  });
}
```

### Event type routing

Route events to appropriate handlers:

```javascript
async function processWebhookEvent(event) {
  switch (event.type) {
    case 'customer.created':
      await handleCustomerCreated(event.payload);
      break;

    case 'customer.status_changed':
      await handleStatusChanged(event.payload);
      break;

    case 'verification.completed':
      await handleVerificationCompleted(event.payload);
      break;

    case 'verification.rfi_required':
      await handleRFIRequired(event.payload);
      break;

    default:
      console.warn(`Unknown event type: ${event.type}`);
  }
}
```

## Testing webhooks

### Local development with ngrok

Test webhooks on localhost using ngrok:

```bash
# 1. Install ngrok
npm install -g ngrok

# 2. Start your local server
node server.js  # Runs on http://localhost:3000

# 3. Create ngrok tunnel
ngrok http 3000

# Output:
# Forwarding: https://abc123.ngrok.io -> http://localhost:3000
```

Use the ngrok URL for webhook configuration:
```
https://abc123.ngrok.io/webhooks/verification
```

### Test event scenarios

Create test customers in sandbox to trigger webhooks:

```javascript
// Test 1: Customer creation webhook
const customer = await createCustomer({
  type: 'INDIVIDUAL',
  email: 'test@example.com',
  firstName: 'Test',
  lastName: 'User'
});
// Expected webhook: customer.created

// Test 2: Status transition to UNDER_REVIEW
await updateCustomer(customer.id, {
  dateOfBirth: '1990-01-01',
  address: { /* ... */ }
});
// Expected webhook: customer.status_changed (CREATED → UNDER_REVIEW)

// Test 3: Force ACTIVE status (sandbox only)
await sandboxSetCustomerStatus(customer.id, 'ACTIVE');
// Expected webhook: verification.completed

// Test 4: Force RFI status
await sandboxSetCustomerStatus(customer.id, 'NEEDS_ADDITIONAL_INFORMATION');
// Expected webhook: verification.rfi_required
```

### Webhook testing checklist

- Endpoint returns 200 OK within 10 seconds
- Signature verification implemented and working
- Timestamp validation prevents old requests
- Idempotency prevents duplicate processing
- All event types handled appropriately
- Error handling and logging in place
- Async processing for slow operations
- HTTPS enabled (production)

## Common integration patterns

### Update customer UI

```javascript
async function handleStatusChanged(payload) {
  const { customerId, newStatus } = payload;

  // Update database
  await db.customers.update(customerId, { status: newStatus });

  // Send real-time update to user's browser
  await websocket.send(customerId, {
    type: 'STATUS_UPDATE',
    status: newStatus
  });

  // Send email notification
  if (newStatus === 'ACTIVE') {
    await sendEmail(payload.email, 'Verification Complete!');
  }
}
```

### Trigger business workflows

```javascript
async function handleVerificationCompleted(payload) {
  const { customerId, newStatus } = payload;

  if (newStatus === 'ACTIVE') {
    // Enable platform features
    await enableTransactions(customerId);
    await enablePaymentMethods(customerId);

    // Start onboarding workflow
    await triggerOnboardingEmail(customerId);

    // Log analytics event
    await analytics.track('customer_verified', { customerId });
  }
}
```

### Handle RFI scenarios

```javascript
async function handleRFIRequired(payload) {
  const { customerId, reason } = payload;

  // Notify customer of required action
  await sendEmail(payload.email, {
    subject: 'Additional Information Required',
    template: 'rfi_notification',
    data: { reason }
  });

  // Create support ticket
  await createSupportTicket({
    customerId,
    type: 'RFI_REQUIRED',
    reason
  });

  // Update UI to show action required
  await updateCustomerPortal(customerId, {
    showRFIBanner: true,
    rfiReason: reason
  });
}
```

## Monitoring and debugging

### Logging best practices

```javascript
app.post('/webhooks/verification', async (req, res) => {
  const eventId = req.body.metadata?.eventId;

  // Log webhook received
  logger.info('Webhook received', {
    eventId,
    type: req.body.type,
    timestamp: new Date().toISOString()
  });

  try {
    // Verify signature
    if (!verifySignature(req.body, req.headers)) {
      logger.warn('Invalid webhook signature', { eventId });
      return res.status(401).json({ error: 'Invalid signature' });
    }

    // Return 200 immediately
    res.status(200).json({ received: true });

    // Process async
    await processWebhookEvent(req.body);

    logger.info('Webhook processed successfully', { eventId });

  } catch (error) {
    logger.error('Webhook processing failed', {
      eventId,
      error: error.message,
      stack: error.stack
    });
  }
});
```

### Metrics to track

Monitor these webhook metrics:

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| **Delivery success rate** | % of webhooks returning 200 | < 95% |
| **Average response time** | Time to return 200 | > 5 seconds |
| **Signature verification failures** | Invalid signature count | > 5/hour |
| **Processing errors** | Failed async processing | > 1% |

### Debugging failed webhooks

If webhooks are not being received:

1. **Check endpoint accessibility:**
   ```bash
   curl -X POST https://your-webhook-url.com/webhooks \
     -H "Content-Type: application/json" \
     -d '{"test": true}'
   ```

2. **Verify HTTPS certificate:** Ensure SSL certificate is valid

3. **Check firewall rules:** Ensure webhook traffic is not blocked

4. **Review logs:** Check application logs for errors

5. **Test signature verification:** Use known-good test data

6. **Contact support:** Request webhook delivery logs

## Security best practices

### Endpoint security

**Do:**
- Always use HTTPS for webhook URLs
- Verify webhook signatures on every request
- Validate timestamp to prevent replay attacks
- Use constant-time comparison for signatures
- Log all webhook attempts for audit trail
- Implement rate limiting to prevent abuse

**Don't:**
- Never skip signature verification
- Don't expose webhook secret in logs
- Don't perform long operations synchronously
- Don't trust webhook data without validation

### Secret management

```javascript
// Good: Use environment variables
const webhookSecret = process.env.WEBHOOK_SECRET;

// Bad: Hardcoded secret
// const webhookSecret = 'abc123...';

// Good: Secrets management service
const webhookSecret = await secretsManager.getSecret('webhook-secret');
```

### Error handling

```javascript
app.post('/webhooks/verification', async (req, res) => {
  try {
    // Verification
    if (!verifySignature(req.body, req.headers)) {
      // Log but don't expose details
      logger.warn('Invalid signature', {
        ip: req.ip,
        timestamp: req.headers['x-webhook-timestamp']
      });
      return res.status(401).json({ error: 'Unauthorized' });
    }

    // Return 200 immediately
    res.status(200).json({ received: true });

    // Process async with error handling
    processWebhookAsync(req.body).catch(err => {
      logger.error('Webhook processing error', {
        error: err.message,
        eventId: req.body.metadata?.eventId
      });
      // Alert operations team
      alertOps('webhook_processing_failure', err);
    });

  } catch (error) {
    // Catch-all for unexpected errors
    logger.error('Webhook handler error', { error: error.message });
    res.status(500).json({ error: 'Internal error' });
  }
});
```

## Troubleshooting

### Webhooks not being received

**Causes:**
- Webhook URL not configured correctly
- HTTPS certificate issues
- Firewall blocking traffic
- Endpoint returning non-200 status

**Solutions:**
1. Verify webhook URL with account manager
2. Test endpoint manually with curl
3. Check SSL certificate validity
4. Review firewall and security group rules

### Signature verification fails

**Causes:**
- Incorrect webhook secret
- Wrong timestamp format
- Payload modification (proxies, middleware)
- Clock skew

**Solutions:**
1. Verify webhook secret with account manager
2. Log and compare expected vs received signatures
3. Check for body parsing middleware issues
4. Sync server time with NTP

### Webhook timeouts

**Causes:**
- Slow synchronous processing
- Database connection issues
- External API calls in handler

**Solutions:**
1. Return 200 OK immediately
2. Move processing to async queue
3. Add timeouts to external calls
4. Optimize database queries

### Duplicate events processed

**Causes:**
- Retry mechanism triggered
- Network issues
- Missing idempotency checks

**Solutions:**
1. Implement idempotency using `eventId`
2. Track processed events in database
3. Add duplicate detection logic

## Frequently asked questions

### How quickly are webhooks delivered?

Webhooks are typically delivered within 5 seconds of the event occurring.

### What happens if my endpoint is down?

The API will retry up to 3 times with exponential backoff. After all retries fail, you can request manual redelivery via support.

### Can I have multiple webhook URLs?

Currently, one webhook URL per organization is supported. Use event type routing to handle events differently.

### How do I test webhooks locally?

Use ngrok or a similar tunneling service to expose your local server to the internet.

### Are webhooks guaranteed to be delivered in order?

No. Webhooks may arrive out of order due to retries and network conditions. Use timestamps to determine event order.

### Can I replay old webhook events?

Contact support to request redelivery of specific events using the `eventId`.

### What if I need to update my webhook URL?

Contact your account manager to update your webhook configuration.
