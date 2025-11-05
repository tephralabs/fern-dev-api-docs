---
title: "Error codes"
description: "Complete reference of API error codes and their meanings"
---

# Error codes

## Overview

This reference guide provides a complete list of error codes returned by the Customer Verification API. Understanding these codes helps you handle errors gracefully, implement robust retry logic, and provide better user experiences.

All API errors follow a consistent JSON format that includes a machine-readable error code, human-readable message, and optional contextual details. Use this reference to diagnose issues, implement proper error handling, and troubleshoot integration problems.

**Related reference guides:**

* For HTTP status code meanings see [HTTP status codes](#http-status-codes)
* For troubleshooting help see [Help & support](../../../overview/help-and-support.md)
* For best practices see [Error handling best practices](#error-handling-best-practices)

---

## Error response format

All API errors follow a consistent JSON format:

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "details": "Additional context about the error",
    "field": "fieldName"
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `code` | string | Machine-readable error code |
| `message` | string | Human-readable error description |
| `details` | string | Additional error context (optional) |
| `field` | string | Field that caused the error (for validation errors) |

---

## HTTP status codes

### Success responses (2xx)

| Status Code | Meaning | Usage |
|-------------|---------|-------|
| **200 OK** | Success | Request completed successfully |
| **201 Created** | Created | Resource created successfully |
| **204 No Content** | Success (no content) | Successful request with no response body |

### Client errors (4xx)

| Status Code | Meaning | Common Causes |
|-------------|---------|---------------|
| **400 Bad Request** | Invalid request | Malformed JSON, missing required fields |
| **401 Unauthorized** | Authentication failed | Invalid or missing API key/token |
| **403 Forbidden** | Permission denied | Insufficient scope/permissions |
| **404 Not Found** | Resource not found | Customer ID doesn't exist |
| **409 Conflict** | Resource conflict | Customer in UNDER_REVIEW cannot be updated |
| **422 Unprocessable Entity** | Validation failed | Invalid field values or format |
| **429 Too Many Requests** | Rate limit exceeded | Too many requests in short period |

### Server errors (5xx)

| Status Code | Meaning | Action |
|-------------|---------|--------|
| **500 Internal Server Error** | Server error | Retry with exponential backoff |
| **503 Service Unavailable** | Service temporarily down | Retry after delay |

---

## Authentication errors

### AUTHENTICATION_FAILED

**Status Code**: 401 Unauthorized

**Cause**: API key or JWT token is invalid, missing, or revoked.

**Response**:
```json
{
  "error": {
    "code": "AUTHENTICATION_FAILED",
    "message": "Invalid API key provided",
    "details": "The API key does not exist or has been revoked"
  }
}
```

**Solutions**:
- Verify API key is correct (no extra spaces)
- Ensure using correct environment (sandbox vs production)
- Check if key has been revoked
- Generate new API key if needed

**Learn more**: [Help & support](../../../overview/help-and-support.md)

---

### TOKEN_EXPIRED

**Status Code**: 401 Unauthorized

**Cause**: JWT token has expired.

**Response**:
```json
{
  "error": {
    "code": "TOKEN_EXPIRED",
    "message": "JWT token has expired",
    "details": "Token expired at 2025-11-05T10:30:00Z"
  }
}
```

**Solutions**:
- Refresh JWT token using your authentication flow
- Retry request with new token

**Learn more**: [Help & support](../../../overview/help-and-support.md)

---

### INSUFFICIENT_PERMISSIONS

**Status Code**: 403 Forbidden

**Cause**: API key or JWT token lacks required scope for the operation.

**Response**:
```json
{
  "error": {
    "code": "INSUFFICIENT_PERMISSIONS",
    "message": "Insufficient permissions for this operation",
    "details": "This operation requires ADMIN or WRITE scope"
  }
}
```

**Solutions**:
- Verify your API key has appropriate permissions
- Contact account manager to request required scope

**Learn more**: [Help & support](../../../overview/help-and-support.md)

---

## Validation errors

### VALIDATION_ERROR

**Status Code**: 400 Bad Request

**Cause**: Request contains invalid or missing required fields.

**Response**:
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request parameters",
    "details": "Field 'email' is required",
    "field": "email"
  }
}
```

**Solutions**:
- Review API documentation for required fields
- Ensure all required fields are present
- Validate field formats before submission

**Learn more**: [Customer API reference](../../../api-reference/customers.md)

---

### INVALID_FIELD_FORMAT

**Status Code**: 422 Unprocessable Entity

**Cause**: Field value doesn't match expected format.

**Response**:
```json
{
  "error": {
    "code": "INVALID_FIELD_FORMAT",
    "message": "Invalid field format",
    "details": "Field 'dateOfBirth' must be in YYYY-MM-DD format",
    "field": "dateOfBirth"
  }
}
```

**Common field format issues**:
- `dateOfBirth`: Must be YYYY-MM-DD format
- `phoneNumber`: Must be E.164 format (+1234567890)
- `email`: Must be valid email format
- `postalCode`: Must match country format

**Solutions**:
- Validate field formats on client side
- Review field format requirements in API docs

---

### INVALID_DOCUMENT_FORMAT

**Status Code**: 422 Unprocessable Entity

**Cause**: Document upload has invalid format or size.

**Response**:
```json
{
  "error": {
    "code": "INVALID_DOCUMENT_FORMAT",
    "message": "Invalid document format",
    "details": "Document must be JPG, PNG, or PDF. Max size: 10MB"
  }
}
```

**Solutions**:
- Use supported formats: JPG, PNG, PDF
- Ensure file size under 10MB
- Encode documents as Base64 strings

**Learn more**: [Document requirements](../guides/kyc-integration.md#document-requirements)

---

## Resource errors

### RESOURCE_NOT_FOUND

**Status Code**: 404 Not Found

**Cause**: Requested resource (customer, transaction, etc.) doesn't exist.

**Response**:
```json
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Customer not found",
    "details": "Customer with ID 'cus_invalid123' does not exist"
  }
}
```

**Solutions**:
- Verify customer ID is correct
- Ensure resource hasn't been deleted
- Check you're using correct environment (sandbox vs production)

---

### RESOURCE_CONFLICT

**Status Code**: 409 Conflict

**Cause**: Operation conflicts with current resource state.

**Response**:
```json
{
  "error": {
    "code": "RESOURCE_CONFLICT",
    "message": "Customer is in UNDER_REVIEW state and cannot be modified",
    "details": "Wait for verification to complete before updating"
  }
}
```

**Common conflicts**:
- Updating customer in UNDER_REVIEW state
- Creating duplicate customer
- Modifying immutable fields

**Solutions**:
- Wait for customer state to change
- Use webhooks to monitor state transitions
- Review customer states documentation

**Learn more**: [Customer states](../concepts/customer-states.md)

---

### DUPLICATE_RESOURCE

**Status Code**: 409 Conflict

**Cause**: Resource with same unique identifier already exists.

**Response**:
```json
{
  "error": {
    "code": "DUPLICATE_RESOURCE",
    "message": "Customer already exists",
    "details": "Customer with email 'user@example.com' already exists"
  }
}
```

**Solutions**:
- Check if customer already exists before creating
- Use idempotency keys for create operations
- Retrieve existing customer instead

**Learn more**: [Idempotency](../api-reference/overview.md#idempotency)

---

## Rate limiting errors

### RATE_LIMIT_EXCEEDED

**Status Code**: 429 Too Many Requests

**Cause**: Too many requests sent in short time period.

**Response**:
```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests",
    "details": "Rate limit exceeded. Retry after 60 seconds",
    "retryAfter": 60
  }
}
```

**Response Headers**:
```http
X-RateLimit-Limit: 300
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1699900060
Retry-After: 60
```

**Solutions**:
- Implement exponential backoff
- Monitor rate limit headers
- Use webhooks instead of polling
- Contact account manager for higher limits

**Learn more**: [Rate limiting](../authentication.md#rate-limiting)

---

## Verification errors

### VERIFICATION_FAILED

**Status Code**: 422 Unprocessable Entity

**Cause**: Customer verification failed due to data issues.

**Response**:
```json
{
  "error": {
    "code": "VERIFICATION_FAILED",
    "message": "Verification failed",
    "details": "Identity document could not be verified"
  }
}
```

**Solutions**:
- Review customer data for accuracy
- Ensure documents are high quality
- Customer may transition to NEEDS_ADDITIONAL_INFORMATION
- Contact support if issue persists

**Learn more**: [Verification issues](../troubleshooting.md#verification-issues)

---

### DOCUMENT_QUALITY_INSUFFICIENT

**Status Code**: 422 Unprocessable Entity

**Cause**: Uploaded document quality is too low for verification.

**Response**:
```json
{
  "error": {
    "code": "DOCUMENT_QUALITY_INSUFFICIENT",
    "message": "Document quality insufficient",
    "details": "Document image is blurry or incomplete"
  }
}
```

**Solutions**:
- Ensure clear, well-lit photos
- Include all four corners of document
- Use minimum 300 DPI resolution
- Avoid glare and shadows
- Upload color images (not black and white)

**Learn more**: [Document upload issues](../troubleshooting.md#document-upload-issues)

---

## Server errors

### INTERNAL_SERVER_ERROR

**Status Code**: 500 Internal Server Error

**Cause**: Unexpected server error occurred.

**Response**:
```json
{
  "error": {
    "code": "INTERNAL_SERVER_ERROR",
    "message": "An unexpected error occurred",
    "details": "Request ID: req_abc123xyz"
  }
}
```

**Solutions**:
- Retry request with exponential backoff
- Check API status page for incidents
- Contact support with request ID if issue persists

**Learn more**: [Getting help](../troubleshooting.md#getting-help)

---

### SERVICE_UNAVAILABLE

**Status Code**: 503 Service Unavailable

**Cause**: Service is temporarily unavailable (maintenance or overload).

**Response**:
```json
{
  "error": {
    "code": "SERVICE_UNAVAILABLE",
    "message": "Service temporarily unavailable",
    "details": "Please retry after a short delay"
  }
}
```

**Solutions**:
- Retry after delay (use exponential backoff)
- Check API status page for maintenance windows
- Monitor for service restoration

---

## Webhook errors

### WEBHOOK_SIGNATURE_INVALID

**Status Code**: 401 Unauthorized

**Cause**: Webhook signature verification failed.

**Response**:
```json
{
  "error": {
    "code": "WEBHOOK_SIGNATURE_INVALID",
    "message": "Webhook signature verification failed",
    "details": "Signature does not match expected value"
  }
}
```

**Solutions**:
- Verify webhook secret is correct
- Use raw request body for signature calculation
- Check timestamp is within 5 minutes
- Ensure proper HMAC-SHA256 implementation

**Learn more**: [Webhook signature verification](../guides/webhooks.md#security-and-signature-verification)

---

### WEBHOOK_DELIVERY_FAILED

**Status Code**: varies

**Cause**: Webhook delivery to your endpoint failed.

**Response**: (In webhook logs, not API response)
```json
{
  "error": {
    "code": "WEBHOOK_DELIVERY_FAILED",
    "message": "Failed to deliver webhook after 3 attempts",
    "details": "Endpoint returned 500 error"
  }
}
```

**Solutions**:
- Ensure webhook endpoint is accessible
- Return 2xx status code within 10 seconds
- Check endpoint logs for errors
- Test endpoint with ngrok locally

**Learn more**: [Webhook troubleshooting](../troubleshooting.md#webhook-issues)

---

## Error handling best practices

### Client-side implementation

{% tabs %}
{% tab title="JavaScript" %}
```javascript
async function makeApiRequest(endpoint, options) {
  try {
    const response = await fetch(endpoint, options);

    // Handle specific status codes
    if (!response.ok) {
      const error = await response.json();

      switch (response.status) {
        case 401:
          // Handle authentication errors
          await refreshAuthToken();
          return makeApiRequest(endpoint, options); // Retry

        case 429:
          // Handle rate limiting
          const retryAfter = response.headers.get('Retry-After') || 60;
          await sleep(retryAfter * 1000);
          return makeApiRequest(endpoint, options); // Retry

        case 500:
        case 503:
          // Handle server errors with exponential backoff
          await exponentialBackoff();
          return makeApiRequest(endpoint, options); // Retry

        default:
          // Log and handle other errors
          console.error('API Error:', error);
          throw new APIError(error);
      }
    }

    return await response.json();
  } catch (error) {
    console.error('Request failed:', error);
    throw error;
  }
}
```
{% endtab %}

{% tab title="Python" %}
```python
import requests
import time

def make_api_request(endpoint, options):
    try:
        response = requests.request(**options, url=endpoint)

        # Handle specific status codes
        if not response.ok:
            error = response.json()

            if response.status_code == 401:
                # Handle authentication errors
                refresh_auth_token()
                return make_api_request(endpoint, options)  # Retry

            elif response.status_code == 429:
                # Handle rate limiting
                retry_after = int(response.headers.get('Retry-After', 60))
                time.sleep(retry_after)
                return make_api_request(endpoint, options)  # Retry

            elif response.status_code in [500, 503]:
                # Handle server errors with exponential backoff
                exponential_backoff()
                return make_api_request(endpoint, options)  # Retry

            else:
                # Log and handle other errors
                print(f"API Error: {error}")
                raise APIError(error)

        return response.json()
    except Exception as error:
        print(f"Request failed: {error}")
        raise
```
{% endtab %}
{% endtabs %}

### Logging for debugging

Always log these details when errors occur:
- **Request ID** (`X-Request-Id` header)
- **Timestamp**
- **HTTP status code**
- **Error code**
- **Error message**
- **Request details** (method, endpoint, sanitized body)

**Example**:
```json
{
  "timestamp": "2025-11-05T10:30:00Z",
  "requestId": "req_abc123xyz",
  "statusCode": 422,
  "errorCode": "INVALID_FIELD_FORMAT",
  "errorMessage": "Invalid field format",
  "endpoint": "POST /api/v1/customers",
  "field": "dateOfBirth"
}
```

### User-friendly error messages

Map API error codes to user-friendly messages:

```javascript
const USER_MESSAGES = {
  AUTHENTICATION_FAILED: 'Authentication failed. Please check your credentials.',
  VALIDATION_ERROR: 'Please check your information and try again.',
  RESOURCE_CONFLICT: 'Your verification is in progress. Please wait.',
  RATE_LIMIT_EXCEEDED: 'Too many requests. Please try again later.',
  INTERNAL_SERVER_ERROR: 'Something went wrong. Please try again.'
};

function getUserMessage(errorCode) {
  return USER_MESSAGES[errorCode] || 'An error occurred. Please try again.';
}
```

---

## Quick reference

### Common error codes

| Error Code | Status | Quick Solution |
|------------|--------|----------------|
| `AUTHENTICATION_FAILED` | 401 | Check API key |
| `TOKEN_EXPIRED` | 401 | Refresh JWT token |
| `VALIDATION_ERROR` | 400 | Fix request data |
| `RESOURCE_NOT_FOUND` | 404 | Verify resource ID |
| `RESOURCE_CONFLICT` | 409 | Wait for state change |
| `RATE_LIMIT_EXCEEDED` | 429 | Implement backoff |
| `INTERNAL_SERVER_ERROR` | 500 | Retry request |

{% hint style="success" %}
Contact support with the request ID and error details for personalized assistance with any error.
{% endhint %}
