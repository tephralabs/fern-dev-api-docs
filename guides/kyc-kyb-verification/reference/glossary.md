---
title: "Glossary"
description: "Definitions of key terms and concepts used in the Customer Verification API"
---

# Glossary

## Overview

This glossary provides definitions of key terms and concepts used throughout the Customer Verification API documentation. Use this reference to understand terminology related to Know Your Customer (KYC) and Know Your Business (KYB) verification, API authentication, customer status, webhooks, and more.

Terms are organized alphabetically for easy lookup. Each definition includes links to relevant documentation where you can learn more about the concept.

{% hint style="info" %}
Can't find a term? Contact [support](../../../overview/help-and-support.md) for clarification.
{% endhint %}

---

## A

### ACTIVE
Customer status indicating verification has been successfully completed and approved. Customers in ACTIVE status can access all platform features and create transactions.

**Learn more**: [Customer statuses](../concepts/customer-statuses.md)

### API Key
Long-lived authentication credential used for server-to-server API requests. API keys provide full access to your account's resources and should be stored securely.

**Learn more**: [Developer dashboard](../../../overview/developer-dashboard.md)

### API Reference
Complete technical documentation of all API endpoints, parameters, and responses.

**Browse**: [Customer API reference](../../../api-reference/customers.md)

---

## B

### Base64 Encoding
Method of encoding binary data (like images) into ASCII text format for transmission via API. Used for document uploads in the Customer Verification API.

**Example**:
```javascript
const documentBase64 = fs.readFileSync('passport.jpg').toString('base64');
```

**Learn more**: [Document upload](../integration-guides/kyc-integration.md)

### Beneficial Owner
Individual who owns 25% or more of a business or exercises control over business operations. Required for Know Your Business (KYB) verification to ensure compliance with regulations.

**Learn more**: [KYB integration](../guides/kyb-integration.md#beneficial-owners)

---

## C

### CREATED
Initial customer status immediately after creation. In this status, customer data can be freely updated, and verification has not yet been initiated.

**Learn more**: [Customer statuses](../concepts/customer-statuses.md#created)

### Customer
An individual (INDIVIDUAL type) or business entity (BUSINESS type) undergoing identity or business verification through the Customer Verification API.

**Learn more**: [Customer API](../api-reference/customers.md)

### Customer ID
Unique identifier assigned to each customer record, formatted as `cus_` followed by alphanumeric characters (e.g., `c65e9fc6-2e65-4db2-b765-0ad5ada68316`).

---

## D

### DEACTIVATED
Customer status indicating the account has been administratively closed. This can occur due to compliance holds, customer requests, or terms of service violations.

**Learn more**: [Customer statuses](../concepts/customer-statuses.md#deactivated)

### DBA (Doing Business As)
Trade name or fictitious business name under which a company operates, different from its legal registered name.

**Example**: "ABC Corp" (legal name) doing business as "Best Services" (DBA)

---

## E

### EIN (Employer Identification Number)
Tax identification number assigned by the IRS to businesses operating in the United States. Required for Know Your Business (KYB) verification of US businesses.

### Endpoint
Specific URL path in the API that accepts requests for a particular operation (e.g., `customers` for creating customers).

**Browse**: [API endpoints](../api-reference/overview.md#api-resources)

### Exponential Backoff
Retry strategy that gradually increases wait time between retry attempts. Recommended for handling rate limits and server errors.

**Example**:
```javascript
async function retryWithBackoff(fn, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      await sleep(Math.pow(2, i) * 1000); // 1s, 2s, 4s
    }
  }
}
```

**Learn more**: [Rate limiting](../troubleshooting.md#rate-limiting)

---

## H

### HMAC-SHA256
Cryptographic hash function used for webhook signature verification. Ensures webhooks originate from the Customer Verification service and haven't been tampered with.

**Learn more**: [Webhook signature verification](../guides/webhooks.md#security-and-signature-verification)

### HTTP Status Code
Three-digit code in API responses indicating the result of a request (e.g., 200 for success, 401 for authentication failure, 429 for rate limiting).

**Full list**: [HTTP status codes](error-codes.md#http-status-codes)

---

## I

### Idempotency
Property ensuring duplicate API requests with the same idempotency key produce the same result, preventing unintended duplicate resource creation.

**Implementation**:
```http
Idempotency-Key: unique-request-identifier-12345
```

**Learn more**: [Idempotency](../api-reference/overview.md#idempotency)

### INDIVIDUAL
Customer type representing a single person undergoing Know Your Customer (KYC) verification. Requires personal information, identity documents, and address verification.

**Learn more**: [KYC integration](../guides/kyc-integration.md)

---

## J

### JWT (JSON Web Token)
Short-lived authentication token used for user-scoped API operations. JSON Web Tokens (JWTs) contain encoded user information and have an expiration time.

**Format**: `header.payload.signature`

**Learn more**: [JWT bearer token authentication](../authentication.md#jwt-bearer-token-authentication)

---

## K

### KYB (Know Your Business)
Business verification process that validates company registration, ownership structure, and beneficial owners to comply with regulations.

**Learn more**: [KYB integration guide](../guides/kyb-integration.md)

### KYC (Know Your Customer)
Identity verification process that validates an individual's identity using government-issued documents, address verification, and compliance checks.

**Learn more**: [KYC integration guide](../guides/kyc-integration.md)

---

## N

### NEEDS_ADDITIONAL_INFORMATION
Customer status indicating the verification provider requires additional information or documents (Request for Information - RFI). Customer data can be updated in this status.

**Learn more**: [Customer statuses](../concepts/customer-statuses.md#needs_additional_information)

### ngrok
Tool for exposing local development servers to the internet via secure tunnels. Useful for testing webhooks locally.

**Usage**:
```bash
ngrok http 3000
```

**Learn more**: [Testing webhooks](../guides/webhooks.md#testing-webhooks)

---

## P

### Pagination
Method of splitting large result sets into smaller pages for efficient data retrieval. The Customer Verification API uses cursor-based pagination.

**Parameters**:
- `limit`: Number of results per page (1-100)
- `cursor`: Pagination cursor from previous response

**Learn more**: [Pagination](../api-reference/overview.md#pagination)

### Production Environment
Live API environment for real customer verification with actual processing times and verification results.

**Base URL**: `https://api.fernhq.com`

**Learn more**: [Environments](../api-reference/overview.md#base-urls)

---

## R

### Rate Limit
Restriction on the number of API requests allowed within a specific time period to ensure fair usage and system stability.

**Tiers**:
- Sandbox: 60/min, 1,000/hour
- Production Standard: 300/min, 10,000/hour
- Production Premium: 1,000/min, 50,000/hour

**Learn more**: [Rate limiting](../authentication.md#rate-limiting)

### REJECTED
Customer status indicating verification permanently failed due to compliance concerns, document issues, or inability to verify identity. Contact support for resolution.

**Learn more**: [Customer statuses](../concepts/customer-statuses.md#rejected)

### Request ID
Unique identifier for each API request, included in response headers as `X-Request-Id`. Essential for debugging and support requests.

**Example**: `req_abc123xyz`

**Learn more**: [Troubleshooting](../troubleshooting.md#getting-help)

### RFI (Request for Information)
Process where verification provider requests additional information or documents to complete verification. Occurs when customer transitions to NEEDS_ADDITIONAL_INFORMATION status.

**Common reasons**:
- Missing or expired documents
- Unclear ownership structure
- Document quality issues
- Insufficient address verification

**Learn more**: [RFI handling](../concepts/customer-statuses.md#needs_additional_information)

---

## S

### SSN (Social Security Number)
Nine-digit tax identification number issued to U.S. citizens and residents. Required for Know Your Customer (KYC) verification of individuals in the United States.

### State Machine
System design where a customer progresses through defined statuses based on verification workflow events. The Customer Verification system uses a 6-status machine.

**Statuses**: CREATED → UNDER_REVIEW → [NEEDS_ADDITIONAL_INFORMATION] → ACTIVE / REJECTED / DEACTIVATED

**Learn more**: [Customer statuses](../concepts/customer-statuses.md)

---

## T

### TIN (Tax Identification Number)
Generic term for tax identification numbers, including Employer Identification Number (EIN) for businesses, Social Security Number (SSN) for individuals, and equivalents in other countries.

---

## U

### UNDER_REVIEW
Customer status indicating verification is actively being processed. Customer data is locked and cannot be updated during this status.

**Typical duration**:
- KYC: Under 10 minutes, up to 3 business days with manual intervention.
- KYB: 3-7 business days

**Learn more**: [Customer statuses](../concepts/customer-statuses.md#under_review)

---

## V

### VAT (Value Added Tax)
Tax identification number used by businesses in many countries outside the United States. Required for Know Your Business (KYB) verification in applicable jurisdictions.

### Verification Workflow
Complete process from customer creation through identity/business verification to approval or rejection.

**Stages**:
1. Customer creation (CREATED)
2. Data submission
3. Verification processing (UNDER_REVIEW)
4. RFI handling (if needed)
5. Final status (ACTIVE, REJECTED, or DEACTIVATED)

**Learn more**: [Verification workflow](../concepts/verification-workflow.md)

---

## W

### Webhook
HTTP callback sent to your server in real-time when events occur (e.g., customer status changes). More efficient than polling the API for updates.

**Benefits**:
- Instant notifications
- Reduced API calls
- Automatic retries
- Scalable architecture

**Learn more**: [Webhook integration](../guides/webhooks.md)

### Webhook Endpoint
HTTPS URL on your server that receives POST requests from Customer Verification webhooks.

**Requirements**:
- Must use HTTPS (not HTTP)
- Return 2xx status code within 10 seconds
- Verify webhook signatures
- Handle retries gracefully

**Learn more**: [Webhook setup](../guides/webhooks.md#setup)

### Webhook Secret
Shared secret key used to generate and verify HMAC-SHA256 signatures for webhook authenticity. Provided when registering webhook URL with account manager.

**Learn more**: [Webhook security](../guides/webhooks.md#security-and-signature-verification)

### Webhook Signature
HMAC-SHA256 hash included in webhook headers to verify the webhook originated from the Customer Verification service and hasn't been tampered with.

**Headers**:
```http
X-Webhook-Signature: abc123...
X-Webhook-Timestamp: 1699900000
```

**Learn more**: [Signature verification](../guides/webhooks.md#signature-verification-implementation)

---

## Common abbreviations

| Abbreviation | Full Term | Description |
|--------------|-----------|-------------|
| **API** | Application Programming Interface | Interface for programmatic access |
| **AML** | Anti-Money Laundering | Compliance regulations |
| **DBA** | Doing Business As | Trade name |
| **EIN** | Employer Identification Number | US business tax ID |
| **HMAC** | Hash-Based Message Authentication Code | Cryptographic signature method |
| **HTTP** | Hypertext Transfer Protocol | Web communication protocol |
| **HTTPS** | HTTP Secure | Encrypted HTTP |
| **ID** | Identifier | Unique reference |
| **JSON** | JavaScript Object Notation | Data format |
| **JWT** | JSON Web Token | Authentication token |
| **KYB** | Know Your Business | Business verification |
| **KYC** | Know Your Customer | Identity verification |
| **PDF** | Portable Document Format | Document file type |
| **RFI** | Request for Information | Additional info request |
| **SDK** | Software Development Kit | Pre-built integration library |
| **SSN** | Social Security Number | US individual tax ID |
| **TIN** | Tax Identification Number | Generic tax ID |
| **URL** | Uniform Resource Locator | Web address |
| **VAT** | Value Added Tax | EU/international tax ID |

{% hint style="success" %}
Need clarification on a term? Contact support or check our comprehensive [FAQ](../faq.md).
{% endhint %}
