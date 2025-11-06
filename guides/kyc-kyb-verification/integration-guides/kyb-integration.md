---
description: Complete end-to-end guide for integrating business customer verification (KYB) into your application
---

# Business customer verification (KYB)

## Overview

Business customer verification, or Know Your Business (KYB), enables you to verify business entities and their beneficial owners to comply with financial regulations. This verification process is required before businesses can access financial services and includes verification of both the business itself and all individuals who own 25% or more of the company or exercise significant control.

This guide walks you through the complete KYB verification flow: creating business profiles, collecting required business information and documents, handling beneficial owner verification, monitoring verification status, and responding to requests for additional information.

KYB verification is more complex than individual verification due to the need to verify multiple stakeholders, understand ownership structures, and collect extensive business formation documents. The process typically takes 3-10 business days, with additional time if more information is needed for complex corporate structures.

**Related customer verification guides:**

* For individual verification see [Individual customer verification (KYC)](kyc-integration.md)
* For webhook integration see [Webhook integration guide](../../webhooks/README.md)
* For customer states reference see [Customer states documentation](../concepts/customer-states.md)

---

## Prerequisites

Before you begin, ensure you have:

* API credentials (API key or JWT token) - see [Get your API credentials](../../../overview/developer-dashboard.md)
* Webhook endpoint configured (recommended) - see [Webhook setup guide](webhooks.md)
* Development environment capable of making HTTPS requests
* File upload handling for business and beneficial owner documents
* Multi-step form implementation for complex business data collection

---

## Step-by-step guide

{% stepper %}
{% step %}
### Create business customer

Create a business customer profile to begin the verification process.

**Endpoint**: `POST customers`

**Sample response:**

```json
{
  "id": "customer_biz123abc456",
  "type": "BUSINESS",
  "status": "CREATED",
  "email": "contact@acmecorp.com",
  "businessName": "Acme Corporation",
  "verificationLink": "https://forms.fernhq.com/verify-customer/:customerID",
  "createdAt": "2025-11-05T10:00:00Z"
}
```

{% hint style="success" %}
Business customer is created with status `CREATED`. Save the `customer.id` - you'll need it for all subsequent steps.
{% endhint %}

{% endstep %}

{% step %}
### Collect required business data

Collect comprehensive business information including company details, ownership structure, and beneficial owners.

**Required information categories:**

* Business profile (legal name, DBA, type, address, contact)
* Tax and registration (tax ID, incorporation date, jurisdiction)
* Business documents (formation documents, licenses, certificates)
* Beneficial owners (individuals with 25%+ ownership or control)
* Financial information (revenue, business purpose)

{% hint style="info" %}
You must collect beneficial owner information for all individuals who own 25% or more of the business OR exercise significant control over the business. Missing or incomplete beneficial owner data is the most common cause of requests for additional information.
{% endhint %}

See the [Required data fields](#required-data-fields) section for complete field definitions and the [Beneficial owner requirements](#beneficial-owner-requirements) section for detailed beneficial owner information.

{% endstep %}

{% step %}
### Submit business data for verification

Once you've collected all required data, submit it using the PATCH endpoint to initiate verification.

**Endpoint:** `PATCH /customers/:customerId`

For complete request/response schemas, see [Customer API reference](../../../api-reference/customers.md).

#### When you can use PATCH

**PATCH is allowed when:**
- Business status is `CREATED` - freely update data before verification starts
- Business status is `NEEDS_ADDITIONAL_INFORMATION` - update requested data to resolve verification issues

**PATCH is blocked when:**
- Business status is `UNDER_REVIEW` - verification in progress, data is locked
- Business status is `ACTIVE`, `REJECTED`, or `DEACTIVATED` - contact support for changes

When PATCH is blocked, the API returns a `PATCH_NOT_ALLOWED` error (400 status code).

#### Using PATCH for business customers

The PATCH endpoint accepts business data including:
- Business information (name, type, tax ID, incorporation details)
- Business address and contact information
- Beneficial owners (all individuals with 25%+ ownership or control)
- Business documents

See [Required data fields](#required-data-fields) for complete field definitions. The API reference provides the complete schema including beneficial owner structure.

**Important notes:**

* Business status automatically transitions to `UNDER_REVIEW` upon successful data submission
* You cannot modify data while status is `UNDER_REVIEW` (verification in progress)
* Missing or incomplete beneficial owner data is the most common cause of requests for additional information
* Always include all beneficial owners in a single submission
* Always handle `PATCH_NOT_ALLOWED` errors gracefully

{% endstep %}

{% step %}
### Monitor verification status

Monitor the business verification status using webhooks.

#### Webhooks

For complete webhook setup instructions see the [Webhook integration guide](../../webhooks/README.md).

{% endstep %}

{% step %}
### Handle requests for additional information

Business verification frequently requires additional information, especially for complex corporate structures or missing beneficial owner documents.

**Common triggers for requests for additional information:**

* Missing beneficial owner data (most common)
* Unclear ownership structure (percentages don't add to 100%)
* Expired business documents
* Poor quality document images
* Conflicting information (address mismatch, name variations)

{% endstep %}

{% step %}
### Verification complete

When business verification is approved, status becomes `ACTIVE` and the business can access your platform's services.

{% endstep %}
{% endstepper %}

---

## Required data fields

Please refer to [Customer API reference](../../../api-reference/customers.md).

---

## Beneficial owner requirements

### Who is a beneficial owner?

A beneficial owner is any individual who:

* Owns 25% or more of the business equity, OR
* Exercises significant control over the business (C-level executives, board members)

{% hint style="info" %}
You must identify and verify all beneficial owners. Missing or incomplete beneficial owner data significantly delays verification and triggers requests for additional information.
{% endhint %}

### Ownership percentage validation

{% hint style="warning" %}
Ownership percentages should generally add up to 100% (or close to it). Significant discrepancies trigger requests for additional information.
{% endhint %}

---

## Document requirements

### Business documents

**Required documents:**

* Certificate of Incorporation or equivalent formation document
* Business License or registration certificate (if applicable)
* Proof of Business Address (bank statement)

**Optional documents** (may speed up verification):

* Articles of Organization
* Operating Agreement or Bylaws
* Tax Registration Certificate
* Professional Licenses (for regulated industries)
* Bank Account Verification Letter

### Business document format

```javascript
{
  type: 'ARTICLES_OF_INCORPORATION',
  description: 'text',               // Text
  documentImage: 'text',             // BASE64 encoded image data
}
```

### Document image requirements

Same requirements as individual verification documents (see KYC Integration Guide), with additional considerations:

* **Multi-page documents**: Upload as single PDF or multiple images
* **Scanned documents**: Ensure scans are clear and complete
* **Certified copies**: Original or certified copies preferred
* **Language**: Documents in non-English languages may require certified translation

---

## Error handling

### Common business verification errors

#### Missing beneficial owner data

**Error:**

```json
{
  "error": "BAD_REQUEST",
  "message": "Validation failed: At least one beneficial owner must be provided",
  "statusCode": 400
}
```

**Solution**: Ensure `beneficialOwners` array has at least one complete entry.

#### Invalid ownership percentages

**Error:**

```json
{
  "error": "BAD_REQUEST",
  "message": "Validation failed: Ownership percentages should total up to a maximum of 100%",
  "statusCode": 400
}
```

**Solution**: Review ownership structure. Percentages should be realistic and add up to no more than 100%.

#### PATCH not allowed

See [Individual customer verification (KYC)](kyc-integration.md) for PATCH restriction handling.

---

## Production checklist

### API integration

* [ ] Authentication working correctly
* [ ] Business customer creation integrated
* [ ] KYB data submission (PATCH) integrated
* [ ] Status monitoring implemented (webhooks)
* [ ] Error handling for all responses
* [ ] Retry logic for transient failures

### Data collection

* [ ] All business fields collected in UI
* [ ] Beneficial owner collection flow implemented
* [ ] Multi-step form for complex data
* [ ] Ownership percentage validation
* [ ] Client-side validation for all fields
* [ ] EIN/tax ID format validation

### Beneficial owner management

* [ ] Dynamic beneficial owner addition (1-N owners)
* [ ] Ownership percentage calculator/validator
* [ ] Document upload for each owner
* [ ] Clear instructions on who qualifies as beneficial owner
* [ ] Corporate structure visualization (optional but helpful)

### Document upload

* [ ] Business document upload
* [ ] Beneficial owner document upload
* [ ] Multi-page PDF support
* [ ] Image quality validation
* [ ] Document type selection UI

### Verification monitoring

* [ ] Webhook endpoint configured
* [ ] Webhook signature verification
* [ ] Status change handling for all states
* [ ] Business contact notifications
* [ ] Request for additional information handling workflow
* [ ] Re-submission flow for additional data

### User experience

* [ ] Clear business onboarding flow
* [ ] Progress tracking across multiple steps
* [ ] Beneficial owner education/guidance
* [ ] Document requirements clearly explained
* [ ] Status dashboard for business verification
* [ ] Request for additional information notifications with specific action items

### Security and compliance

* [ ] API keys stored securely
* [ ] HTTPS for all requests
* [ ] Business PII encrypted
* [ ] Beneficial owner PII encrypted
* [ ] Document images stored securely
* [ ] Access logging and audit trail
* [ ] Data retention policies

### Testing

* [ ] All test scenarios passed in sandbox
* [ ] Success flow tested
* [ ] Request for additional information flow tested
* [ ] Complex ownership tested
* [ ] Multiple beneficial owner scenarios tested
* [ ] Edge cases covered

### Monitoring and support

* [ ] Error logging
* [ ] Status transition tracking
* [ ] Customer support workflow
* [ ] Escalation path for complex cases
* [ ] Metrics tracking (conversion rates, time to verify, request for additional information rates)

---

## Related resources

* For webhook setup see [Webhook integration guide](../../webhooks/README.md)
* For customer state definitions see [Customer states documentation](../concepts/customer-states.md)
* For individual verification see [Individual customer verification (KYC)](kyc-integration.md)
* For API reference see [Customer API reference](../../../api-reference/customers.md)
* For troubleshooting see [Help & support](../../../overview/help-and-support.md)
