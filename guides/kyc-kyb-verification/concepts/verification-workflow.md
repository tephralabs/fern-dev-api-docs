# Verification workflow

## Overview

This guide explains the complete verification process from submission to approval for both individual (KYC) and business (KYB) customers. The verification workflow combines data validation, document verification, and compliance screening to ensure customer identity and meet regulatory requirements.

Understanding this workflow helps you build better integrations, set appropriate customer expectations, and handle various verification scenarios effectively. The process involves multiple verification providers working in parallel to provide comprehensive identity and compliance checks.

**Related guides:**

* For verification states see [Customer states](customer-states.md)
* For webhook notifications see [Webhook integration](../../webhooks/README.md)
* For API operations see [Customer API](../../../api-reference/customers.md)

## What is verification

Customer verification is the process of confirming identity and compliance requirements before granting access to financial services. This process involves:

1. **Data collection:** Gathering customer information and documents
2. **Submission:** Sending data to verification providers
3. **Review:** Automated and manual verification checks
4. **Decision:** Approval, rejection, or request for additional information
5. **Activation:** Granting platform access upon approval

## Common verification scenarios

### Straightforward approval

**Flow:**
1. Customer submits complete, valid data
2. Verification is complete under 10 minutes for KYC
4. Status: `UNDER_REVIEW` → `ACTIVE`

**Developer experience:**
- Single webhook: status change to `ACTIVE`
- Total time: Under 10 minutes
- No additional action required

### Request for information (RFI)

**Flow:**
1. Customer submits data
2. Verification identifies issue (e.g., blurry document image)
3. Status: `UNDER_REVIEW` → `NEEDS_ADDITIONAL_INFORMATION`
4. Customer uploads better quality image
5. System re-submits automatically
6. Verification is run again → Approves
7. Status: `NEEDS_ADDITIONAL_INFORMATION` → `UNDER_REVIEW` → `ACTIVE`

**Developer experience:**
- Multiple webhooks: RFI notification, status changes
- Total time: up to under 15 minutes, excluding the time for the customer to provide new information (including RFI resolution)
- Action required: Facilitate customer data update

**Common RFI reasons:**
- Document image quality insufficient
- Address cannot be verified
- Expired documents
- Missing beneficial owner information (KYB)
- Ownership percentages do not add to 100% (KYB)

### Rejection

**Flow:**
1. Customer submits data
2. Provider identifies unresolvable issue
3. Multiple RFI attempts fail to resolve issue
4. Manual review: Permanent rejection
5. Status: `UNDER_REVIEW` → `NEEDS_ADDITIONAL_INFORMATION` → `REJECTED`

**Developer experience:**
- Final rejection webhook
- Customer cannot use platform
- Support contact required for appeal or clarification

**Common rejection reasons:**
- Identity documents could not be verified
- Sanctions or watchlist match
- High-risk jurisdictions
- Fraudulent information detected
- Compliance policy violations

## Monitoring verification progress

### Webhooks

**Setup:** Configure webhook endpoint to receive real-time notifications

**Benefits:**
- Real-time status updates
- No polling required
- Efficient resource usage
- Immediate response to status changes

**Learn more:** [Webhook integration guide](../../webhooks/README.md)

## Best practices

### For developers

1. **Use webhooks:** Real-time notifications are more efficient than polling
2. **Implement idempotency:** Handle duplicate webhook deliveries gracefully
3. **Validate data before submission:** Reduce RFI likelihood with client-side validation
4. **Set user expectations:** Inform users about typical verification timelines
5. **Handle all states:** Build UI for CREATED, UNDER_REVIEW, RFI, ACTIVE, and REJECTED
6. **Log everything:** Maintain audit logs of verification attempts and status changes
7. **Test error paths:** Test RFI and rejection flows, not just happy path

### For data quality

1. **Document quality matters:** High-resolution, clear images reduce RFI rate
2. **Complete data upfront:** Provide all required fields to avoid delays
3. **Validate address formats:** Use address validation APIs before submission
4. **Double-check beneficial owners:** Missing UBOs are the number one KYB RFI cause
