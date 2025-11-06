---
title: "KYC Integration Guide"
description: "Complete end-to-end guide for integrating individual customer verification (KYC) into your application"
---

# KYC integration guide

## Overview

Know Your Customer (KYC) verification is the process of verifying the identity of individual customers to comply with anti-money laundering (AML) and financial regulations. This comprehensive guide walks you through integrating KYC verification for individual customers into your application, from creating customer profiles to handling document uploads and monitoring verification status.

By the end of this guide, you'll understand how to create customers, collect required data, submit information for verification, monitor verification status, and respond to requests for additional information. The entire process typically takes 1-7 business days from initial submission to approval.

This integration enables you to verify customer identities before they access financial services, ensuring compliance while maintaining a smooth user experience. You'll implement a complete verification flow using API endpoints, webhooks, and document uploads.

**Related customer verification guides:**

* For business verification see [KYB integration guide](kyb-integration.md)
* For webhook setup see [Webhook integration guide](../../webhooks/README.md)
* For customer state definitions see [Customer states](../concepts/customer-states.md)
* For API reference see [Customer API reference](../../../api-reference/customers.md)

---

## Prerequisites

Before you begin, ensure you have:

- API credentials (API key or JWT token) - [Get credentials](../../../overview/developer-dashboard.md)
- Webhook endpoint configured (recommended) - [Webhook setup guide](webhooks.md)
- Sandbox environment access for testing
- Development environment capable of making HTTPS requests
- File upload handling (for document images/PDFs)

**Estimated integration time**: 4-6 hours for complete implementation

{% hint style="info" %}
This guide uses placeholder URLs like `api.fernhq.com`. Replace these with your actual API base URLs when implementing.
{% endhint %}

---

## Verification timeline

Understanding the typical timeline helps set proper expectations:

- **Data collection**: Immediate (user fills form in your application)
- **Verification processing**: Under 10 minutes after submission
- **Additional information requests**: May add 2-3 days if documents need clarification
- **Total time**: Under 15 minutes from initial submission to approval

### Customer state flow

```
CREATED → UNDER_REVIEW → ACTIVE
          ↓
          NEEDS_ADDITIONAL_INFORMATION → (update data) → UNDER_REVIEW
```

For complete state definitions, see [Customer states](../concepts/customer-states.md).

---

## Step-by-step integration

{% stepper %}
{% step %}
### Create individual customer

Create a customer profile to begin the verification process. This initial step registers the customer in your system with basic information.

<!-- PHASE6_API_UPDATE: RESPONSE_SCHEMA - Validate customer creation response -->
**Sample response:**
```json
{
  "id": "customer_abc123def456",
  "type": "INDIVIDUAL",
  "status": "CREATED",
  "email": "jane.smith@example.com",
  "firstName": "Jane",
  "lastName": "Smith",
  "verificationLink": "https://forms.fernhq.com/verify-customer/:customerID",,
  "createdAt": "2025-11-05T10:00:00Z"
}
```
<!-- /PHASE6_API_UPDATE -->

{% hint style="success" %}
Customer is created with status `CREATED`. Save the `customer.id` - you'll need it for the next steps.
{% endhint %}
{% endstep %}

{% step %}
### Collect required KYC data

Collect the required personal information, address, and documents from your customer. You can build a custom form in your application or direct customers to the `verificationLink` from Step 1.

**Required information:**
* Legal name (first, last, middle)
* Date of birth
* Phone number (international format)
* Complete residential address
* National ID information
* Employment and financial details
* Government-issued ID documents

See [Required data fields](#required-data-fields) section for complete field definitions.

{% hint style="info" %}
For the best user experience, collect information progressively rather than requiring all fields at once. Start with basic information, then request documents after initial data validation.
{% endhint %}
{% endstep %}

{% step %}
### Submit KYC data for verification

Once you've collected all required data, submit it using the PATCH endpoint to initiate verification.

{% hint style="warning" %}
Customer status will automatically transition to `UNDER_REVIEW` upon successful data submission. You cannot modify data while status is `UNDER_REVIEW` (verification in progress). Always handle `PATCH_NOT_ALLOWED` errors gracefully.
{% endhint %}
{% endstep %}

{% step %}
### Monitor verification status

Monitor the customer's verification status using webhooks.

See the complete [Webhook integration guide](../../webhooks/README.md) for setup instructions.


### Handle requests for additional information

When verification status becomes `NEEDS_ADDITIONAL_INFORMATION`, the verification provider needs additional documents or clarifications.

**What to do:**

1. Refer to webhook notifications for more detail on what is required
2. Collect additional data from customer
3. Resubmit updated data using PATCH

{% hint style="info" %}
Request for Additional Information (RFI) typically occurs when document quality is poor or information doesn't match across sources. Providing clear instructions to customers upfront reduces RFI rates.
{% endhint %}
{% endstep %}

{% step %}
### Verification complete

When verification is approved, customer status becomes `ACTIVE` and the customer can access your platform's services.

{% hint style="success" %}
Once a customer is `ACTIVE`, they remain verified unless circumstances require re-verification. Store the customer ID for all future transactions.
{% endhint %}
{% endstep %}
{% endstepper %}

---

## Required data fields

### Personal information

| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `legalFirstName` | string | Yes | Legal first name (as appears on ID) | "Jane" |
| `legalLastName` | string | Yes | Legal last name (as appears on ID) | "Smith" |
| `legalMiddleName` | string | No | Legal middle name | "Marie" |
| `phoneNumber` | string | Yes | Phone in E.164 format | "+14155551234" |
| `dateOfBirth` | string | Yes | ISO 8601 date (YYYY-MM-DD) | "1990-05-15" |
| `nationality` | string | Yes | ISO 3166-1 alpha-2 country code | "US" |

### Address

| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `streetLine1` | string | Yes | Street address | "123 Main Street" |
| `streetLine2` | string | No | Apartment, suite, unit | "Apt 4B" |
| `city` | string | Yes | City name | "San Francisco" |
| `stateRegionProvince` | string | Yes | 2-letter state code | "CA" |
| `postalCode` | string | Yes | Zip/postal code | "94102" |
| `countryCode` | string | Yes | ISO 3166-1 alpha-2 code | "US" |
| `locale` | string | No | Language and region | "en-US" |

### National identification

**Note:** When patching this data in, all three fields are required together in a single patch.

| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `nationalIdType` | string | Yes | Type of ID | "SSN", "TIN", "NATIONAL_ID" |
| `nationalIdNumber` | string | Yes | ID number | "123-45-6789" |
| `nationalIdIssuingCountry` | string | Yes | Country that issued ID | "US" |

### Employment and financial

| Field | Type | Required | Description | Valid Values |
|-------|------|----------|-------------|--------------|
| `employmentStatus` | string | Yes | Current employment status | "EMPLOYED", "SELF_EMPLOYED", "UNEMPLOYED", "RETIRED", "STUDENT" |
| `mostRecentOccupation` | string | Yes | Job title or occupation | "Software Engineer", "Teacher", etc. |
| `sourceOfFunds` | string | Yes | Primary income source | "EMPLOYMENT", "BUSINESS", "INVESTMENTS", "INHERITANCE", "SAVINGS" |
| `accountPurpose` | string | Yes | How account will be used | "PERSONAL_USE", "BUSINESS_USE", "INVESTMENT" |
| `expectedMonthlyPaymentsUsd` | string | Yes | Expected monthly volume | "UNDER_10K", "10K_TO_50K", "50K_TO_100K", "OVER_100K" |
| `isIntermediary` | boolean | Yes | Acting on behalf of others | `true` or `false` |

### Documents

At least one government-issued ID document is required. See [Document requirements](#document-requirements) for details.

---

## Document requirements

### Required document types

**Minimum**: 1 government-issued photo ID

Please refer to [Customer API reference](../../../api-reference/customers.md) for more detail.

**Accepted ID types:**
* GOVERNMENT_ID
* PROOF_OF_ADDRESS

**Sample of Accepted ID subtypes:**
* PASSPORT
* NATIONAL_ID

**Additional documents** (recommended to speed up verification):
* Proof of Address (bank statement)

### Document format

<!-- PHASE6_API_UPDATE: REQUEST_PAYLOAD - Validate document structure -->
```javascript
{
  type: 'GOVERNMENT_ID',         // Document type (see types above)
  subtype: 'PASSPORT',           // Document subtype (see types above)
  documentIdNumber: 'P12345678', // Document number
  countryCode: 'US',             // ISO 3166-1 alpha-2 code
  issuanceDate: '2030-12-31',    // ISO 8601 date
  expirationDate: '2030-12-31',  // ISO 8601 date
  frontIdImage: 'text',          // BASE64 encoded image data
  backIdImage: 'text'            // BASE64 encoded image data
  proofOfAddressImage: 'text'    // BASE64 encoded image data
  description: 'text'    // Notes about the document
}
```
<!-- /PHASE6_API_UPDATE -->

### Document image requirements

{% hint style="warning" %}
Poor quality images are the number one cause of verification delays. Ensure all images meet the requirements below.
{% endhint %}

**Technical requirements:**
* **Format**: JPG, PNG, or PDF
* **Size**: 100 KB - 10 MB per file
* **Resolution**: Minimum 200x200 pixels
* **Color**: Full color advised (no black and white)

**Quality guidelines:**
* Clear and legible - All text must be readable
* Well-lit - No shadows or glare
* Complete - All four corners visible
* Unobstructed - No fingers or objects covering document
* Current - Not expired
* Avoid blurry, dark, cropped, or screenshot images

---

## Error handling

### Common errors

#### PATCH not allowed (400)

<!-- PHASE6_API_UPDATE: ERROR_CODES - Verify error response structure -->
**Error:**
```json
{
  "error": "PATCH_NOT_ALLOWED",
  "message": "Cannot update KYC data: verification is currently in progress",
  "statusCode": 400
}
```
<!-- /PHASE6_API_UPDATE -->

**Cause**: Attempting to update customer data while status is `UNDER_REVIEW`, `ACTIVE`, or `DEACTIVATED`.

**Solution**: Wait for verification to complete. Check customer status before attempting PATCH.

#### Validation errors (400)

<!-- PHASE6_API_UPDATE: ERROR_CODES - Verify validation error structure -->
**Error:**
```json
{
  "error": "BAD_REQUEST",
  "message": "Validation failed: phoneNumber must be in E.164 format",
  "statusCode": 400
}
```
<!-- /PHASE6_API_UPDATE -->

**Cause**: Invalid data format (e.g., phone number not in E.164 format, invalid date format).

**Solution**: Validate data client-side before submission:

#### Authentication errors (401)

<!-- PHASE6_API_UPDATE: ERROR_CODES - Verify authentication error structure -->
**Error:**
```json
{
  "error": "UNAUTHORIZED",
  "message": "Invalid or expired API key",
  "statusCode": 401
}
```
<!-- /PHASE6_API_UPDATE -->

**Solution**: Verify your API key is correct and not expired. Implement token refresh logic for JWT tokens.

---

## Production checklist

Before going live with KYC verification:

### API integration
- [ ] Authentication working correctly (API key or JWT)
- [ ] Customer creation endpoint integrated
- [ ] KYC data submission (PATCH) endpoint integrated
- [ ] Status monitoring implemented (webhooks or polling)
- [ ] Error handling for all API responses
- [ ] Retry logic for transient failures

### Data collection
- [ ] All required fields collected in your UI
- [ ] Client-side validation for data formats
- [ ] Phone number validation (E.164 format)
- [ ] Date validation (ISO 8601 YYYY-MM-DD)
- [ ] Address completeness validation
- [ ] Age verification (18+ requirement)

### Document upload
- [ ] Image quality validation (size, resolution, format)
- [ ] Support for multiple document types

### Verification monitoring
- [ ] Webhook endpoint configured (if using webhooks)
- [ ] Webhook signature verification implemented
- [ ] Status change handling for all states
- [ ] Customer notifications for status changes
- [ ] RFI handling workflow implemented

### User experience
- [ ] Clear instructions for data collection
- [ ] Progress indicators during submission
- [ ] Real-time validation feedback
- [ ] Document upload guidance and tips
- [ ] Status updates communicated to customers
- [ ] RFI notifications with clear next steps

### Security and compliance
- [ ] API keys stored securely (environment variables)
- [ ] HTTPS used for all API requests
- [ ] Customer PII encrypted in transit and at rest
- [ ] Document images stored securely
- [ ] Access logging for audit trail
- [ ] Data retention policies implemented

### Testing
- [ ] All test scenarios passed in sandbox
- [ ] Success flow tested end-to-end
- [ ] RFI flow tested and validated
- [ ] Rejection flow handled gracefully
- [ ] Edge cases tested (network failures, timeouts)
- [ ] Performance testing completed

### Monitoring and support
- [ ] API error logging implemented
- [ ] Status transition logging
- [ ] Customer support workflow for issues
- [ ] Escalation path for stuck verifications
- [ ] Metrics tracking (conversion rates, time to verify)

---

## Next steps

* Set up webhooks - [Webhook integration guide](../../webhooks/README.md)
* Understand customer states - [Customer states documentation](../concepts/customer-states.md)
* Business verification - [KYB integration guide](kyb-integration.md)
* API reference - [Customer API reference](../../../api-reference/customers.md)
* Get help - [Help & support](../../../overview/help-and-support.md)
