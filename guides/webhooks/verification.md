# Verification

### Webhook Verification

To ensure that incoming webhook requests are genuinely from Fern and have not been tampered with, you must verify the signature included with each webhook. Fern signs each webhook payload using a secret (the one associated with your subscription) and provides the signature and related information in the request headers. By validating the signature (and checking the timestamp), your application can confirm the authenticity and freshness of the webhook data before processing it.

#### Signature Headers

Each webhook HTTP request includes the following headers to assist with verification:

* `x-api-signature` – The HMAC SHA-256 signature of the payload (hex-encoded). This is what you will compare against your own computed signature.
* `x-api-timestamp` – The UNIX timestamp (in seconds or milliseconds) at which the request was sent. This is used in the signature computation and to prevent replay attacks.

Make sure to record the `x-api-timestamp` and ensure it is within an acceptable range (for example, within 1 minutes of your server’s current time) before trusting the webhook. If the timestamp is too far out-of-sync, it could indicate a replay attack or an extremely delayed request, and you may want to ignore it. Also, use the payload `id` to guard against processing the same event twice.

#### Signature Generation (TypeScript)

Fern generates the `x-api-signature` header by computing an HMAC using SHA-256. The signing string is composed of the timestamp and the request body, separated by a dot. In pseudo-code, Fern does:

```
stringToSign = "<timestamp>.<raw_body>"
signature = HMAC_SHA256(secret, stringToSign)
```

The resulting signature is encoded as a hexadecimal string and sent in the header. Important: The exact raw body of the request must be used in this computation. Do not modify the body (even pretty-printing JSON or changing whitespace) before computing the HMAC on your side, as any change will alter the signature.

Here’s an example of how to generate the signature in TypeScript (Node.js environment) using the secret, timestamp, and body:

```
import * as crypto from "crypto";

const generateSignature = (
  body: string,       // raw JSON string of the request body
  timestamp: string,  // timestamp string from header
  secret: string      // your webhook secret
): string => {
  const payloadToSign = `${timestamp}.${body}`;
  return crypto
    .createHmac("sha256", secret)
    .update(payloadToSign)
    .digest("hex");
};
```

In this code, we concatenate the timestamp and the body with a period and then create an HMAC using the `SHA-256` algorithm with your webhook secret. The output is a hexadecimal string. Fern performs this same computation for each webhook it sends. Your goal in verification is to perform the same computation and compare the result with the signature provided by Fern.

#### Signature Validation (TypeScript)

To validate a webhook in your handler, follow these steps:

1. **Retrieve the headers and body**: Get the `x-api-signature` and `x-api-timestamp` from the request headers, and get the raw request body string.
2. **Recompute the signature**: Use your secret and the received timestamp and body to compute the expected signature (using a function like `generateSignature` above).
3. **Compare signatures securely**: Check if the computed signature matches the X-Api-Signature from the header. Use a constant-time comparison to avoid timing attacks.
4. **Check timestamp freshness**: Verify that the timestamp from `x-api-timestamp` is recent (e.g., within the last few minutes). If it’s too old or far in the future, you may reject the request as a replay attempt.
5. **Process the webhook if valid**: Only if the signature is valid and the timestamp is acceptable should you proceed to process the webhook (e.g., update your database or trigger other actions). If verification fails, you should reject the request (e.g., respond with 4xx status) or ignore it.\


Below is an example implementation in TypeScript for validating the signature of a request:

```
import * as crypto from "crypto";

const isValidSignature = (
  body: string,       // raw JSON string of the request body
  timestamp: string,  // timestamp string from X-Api-Timestamp header
  signature: string,  // hex string from X-Api-Signature header
  secret: string      // your webhook secret
): boolean => {
  const expectedSignature = generateSignature(body, timestamp, secret);
  // Use timing-safe comparison to avoid timing attack vulnerability
  const sigBuffer = Buffer.from(signature, "hex");
  const expectedSigBuffer = Buffer.from(expectedSignature, "hex");
  return (
    sigBuffer.length === expectedSigBuffer.length &&
    crypto.timingSafeEqual(sigBuffer, expectedSigBuffer)
  );
};

// Example usage in an Express.js route handler
app.post("/my-webhook-endpoint", express.raw({ type: "application/json" }), (req, res) => {
  const signature = req.header("x-api-signature");
  const timestamp = req.header("x-api-timestamp");
  const rawBody = req.body.toString(); // assuming body is kept as raw Buffer

  if (!signature || !timestamp || !isValidSignature(rawBody, timestamp, signature, WEBHOOK_SECRET)) {
    console.error("Invalid webhook signature – request possibly forged!");
    return res.sendStatus(400); // reject if signature doesn't match
  }

  // Optionally, also check the timestamp here for staleness
  const now = Date.now();
  const reqTime = Number(timestamp);
  if (isNaN(reqTime) || Math.abs(now - reqTime*1000) > 5 * 60 * 1000) {  // example: 5 minute tolerance
    console.error("Webhook timestamp outside of tolerance – possible replay attack!");
    return res.sendStatus(400);
  }

  // Signature is valid and timestamp is recent – process the webhook
  const event = JSON.parse(rawBody);
  handleWebhookEvent(event);
  res.sendStatus(200);
});
```

In the above example, `generateSignature` is the function defined in the previous section. We compare the signatures using `crypto.timingSafeEqual` which helps prevent timing attacks by making sure the comparison takes the same amount of time regardless of how much of the string matches. We also ensure the buffers are of equal length before comparing (as a safety check).
