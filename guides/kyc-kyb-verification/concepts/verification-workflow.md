# Verification workflow

## Overview

This guide explains the complete verification process from submission to approval for both individual (KYC) and business (KYB) customers. The verification workflow combines data validation, document verification, and compliance screening to ensure customer identity and meet regulatory requirements.

Understanding this workflow helps you build better integrations, set appropriate customer expectations, and handle various verification scenarios effectively. The process involves multiple verification providers working in parallel to provide comprehensive identity and compliance checks.

**Related guides:**

* For verification states see [Customer states](customer-states.md)
* For webhook notifications see [Webhook integration](../integration-guides/webhooks.md)
* For API operations see [<!-- PHASE6_API_REFERENCE: Customer API -->](../../../api-reference/customers.md)

## What is verification

Customer verification is the process of confirming identity and compliance requirements before granting access to financial services. This process involves:

1. **Data collection:** Gathering customer information and documents
2. **Submission:** Sending data to verification providers
3. **Review:** Automated and manual verification checks
4. **Decision:** Approval, rejection, or request for additional information
5. **Activation:** Granting platform access upon approval

### Verification providers

The API uses multiple verification providers to ensure comprehensive identity and compliance checks. Each provider specializes in different verification aspects:

- Identity verification
- Document authentication
- Sanctions and watchlist screening
- Anti-money laundering (AML) checks
- Business entity verification

{% hint style="success" %}
Using multiple providers improves accuracy and reduces false rejections by cross-validating information.
{% endhint %}

## KYC workflow (individuals)

### Overview

The KYC (Know Your Customer) workflow verifies individual customers through a 7-step process combining data validation, document verification, and compliance screening.

### Complete KYC flow

```
┌──────────────────────────────────────────────────────────────┐
│ Step 1: Customer Creation                                    │
│ Developer creates customer record                            │
│ Status: CREATED                                              │
└──────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────────┐
│ Step 2: Data Collection                                      │
│ Customer submits required information:                       │
│ - Personal details (name, DOB, address)                      │
│ - Government ID documents                                    │
│ - Financial information                                      │
└──────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────────┐
│ Step 3: Validation & Verification Gate                       │
│ System validates data completeness:                          │
│ - All required fields present                                │
│ - Valid document types provided                              │
│ - Data format correctness                                    │
└──────────────────────────────────────────────────────────────┘
                          │
                    ┌─────┴─────┐
                    │  Pass?    │
                    └─────┬─────┘
              Yes ────────┼──────── No
                          │            │
                          │            └──► Remain CREATED
                          │                (collect more data)
                          ▼
┌──────────────────────────────────────────────────────────────┐
│ Step 4: Verification Submission                              │
│ Data sent to verification providers (parallel)               │
│ Status: UNDER_REVIEW                                         │
└──────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────────┐
│ Step 5: Provider Review (1-5 business days)                  │
│ Providers perform:                                           │
│ - Identity verification                                      │
│ - Document authentication                                    │
│ - AML/sanctions screening                                    │
│ - Risk assessment                                            │
└──────────────────────────────────────────────────────────────┘
                          │
                ┌─────────┴─────────┐
                │                   │
         Approved                  Rejected/RFI
                │                   │
                ▼                   ▼
┌──────────────────────────┐  ┌──────────────────────────┐
│ Step 6A: Approval        │  │ Step 6B: RFI Required    │
│ Verification credentials │  │ Additional info needed   │
│ granted                  │  │ Status: NEEDS_ADDITIONAL │
└──────────────────────────┘  │ _INFORMATION             │
                │             └──────────────────────────┘
                │                         │
                │                         │
                │                         │ Customer
                │                         │ updates data
                │                         │
                │                         ▼
                │             ┌──────────────────────────┐
                │             │ Re-submission            │
                │             │ Returns to Step 4        │
                │             └──────────────────────────┘
                │
                ▼
┌──────────────────────────────────────────────────────────────┐
│ Step 7: Activation                                           │
│ All providers approve                                        │
│ Status: ACTIVE                                               │
│ Customer can use platform                                    │
└──────────────────────────────────────────────────────────────┘
```

### Step-by-step KYC process

{% stepper %}
{% step %}
### Create customer record

Developer creates customer record via API.

**Endpoint:** <!-- PHASE6_API_REFERENCE: POST /api/v1/customers -->

**Sample request:**

{% tabs %}
{% tab title="cURL" %}
```sh
curl --location 'https://api.example.com/v1/customers' \
--header 'Authorization: Bearer <API_TOKEN>' \
--header 'Content-Type: application/json' \
--data '{
  "type": "INDIVIDUAL",
  "email": "john@example.com",
  "firstName": "John",
  "lastName": "Doe"
}'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const response = await fetch('https://api.example.com/v1/customers', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer <API_TOKEN>',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    type: 'INDIVIDUAL',
    email: 'john@example.com',
    firstName: 'John',
    lastName: 'Doe'
  })
});

const customer = await response.json();
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

response = requests.post(
    'https://api.example.com/v1/customers',
    headers={'Authorization': 'Bearer <API_TOKEN>'},
    json={
        'type': 'INDIVIDUAL',
        'email': 'john@example.com',
        'firstName': 'John',
        'lastName': 'Doe'
    }
)

customer = response.json()
```
{% endtab %}
{% endtabs %}

**Result:** Customer record created with status `CREATED`
{% endstep %}

{% step %}
### Collect customer data

Customer completes verification form with required information.

**Required data:**
- Legal name (first, middle, last)
- Date of birth
- Phone number (international format)
- Complete residential address
- National ID information
- Employment and financial details
- Government-issued ID documents (at least one)

**Collection methods:**
1. **Hosted form:** Use the `verificationLink` returned in Step 1
2. **Custom integration:** Collect data in your application and submit via API

{% hint style="info" %}
Providing complete, high-quality data upfront reduces the likelihood of RFI (Request for Information) and speeds up verification.
{% endhint %}
{% endstep %}

{% step %}
### Validation and verification gate

System validates data completeness before submission.

**Validation checks:**
- All required fields present and non-empty
- Address is complete (street, city, state, postal code, country)
- At least one government ID document provided
- Document images are accessible
- Data formats are correct (e.g., date of birth is valid)

**Outcomes:**
- **Pass:** Proceeds to verification submission
- **Fail:** Customer remains in `CREATED` status, more data needed

{% hint style="warning" %}
The verification gate prevents incomplete submissions that would likely be rejected. Ensure all required fields are collected before submission.
{% endhint %}
{% endstep %}

{% step %}
### Verification submission

System submits customer data to verification providers.

**Process:**
1. Data formatted for each provider's API
2. Submissions sent in parallel to all providers
3. Customer status automatically changes to `UNDER_REVIEW`
4. Webhook sent to your endpoint notifying status change

**Status change:** `CREATED` → `UNDER_REVIEW`

{% hint style="success" %}
Verification submission happens automatically when data passes the verification gate. No additional API calls required.
{% endhint %}
{% endstep %}

{% step %}
### Provider review

Verification providers review customer data.

**Review process:**

**Automated checks** (seconds to minutes):
- Identity verification against government databases
- Document authenticity verification (OCR, security features)
- Sanctions and watchlist screening
- PEP (Politically Exposed Person) screening

**Manual review** (if needed):
- Complex cases requiring human review
- Document quality assessment
- Risk analysis

**Duration:** 1-5 business days (typical)

{% hint style="info" %}
Most verifications complete within 1-3 business days, but complex cases may take up to 5 business days. Use webhooks to receive notifications when decisions are made.
{% endhint %}
{% endstep %}

{% step %}
### Approval or RFI

Provider makes decision based on review.

**If approved:**
1. Provider sends approval webhook to API
2. Verification credential granted to customer
3. System checks if all required credentials present
4. If yes: Customer status → `ACTIVE`
5. If no: Customer status remains `UNDER_REVIEW` (waiting for other providers)

**Sample webhook:**
```json
{
  "type": "customer.status_changed",
  "payload": {
    "customerId": "cus_abc123",
    "previousStatus": "UNDER_REVIEW",
    "newStatus": "ACTIVE",
    "updatedAt": "2025-11-05T10:30:00Z"
  }
}
```

**If RFI required:**
1. Provider sends RFI webhook to API
2. Customer status → `NEEDS_ADDITIONAL_INFORMATION`
3. Customer can update data via PATCH endpoint
4. System re-evaluates and re-submits automatically
5. Returns to provider review

{% hint style="success" %}
RFI is a recoverable state. Most RFI cases can be resolved by providing the requested information or clarifications.
{% endhint %}
{% endstep %}

{% step %}
### Activation

All providers approve verification.

**Requirements:**
- All required verification credentials granted
- No pending RFI requests
- No rejections from any provider

**Process:**
1. System confirms all credentials present
2. Customer status → `ACTIVE`
3. Webhook sent to your endpoint
4. Customer can now access platform features

**Developer actions:**
```javascript
// Listen for ACTIVE status
if (customer.status === 'ACTIVE') {
  // Enable platform features
  await enableTransactions(customer.id);
  await grantAPIAccess(customer.id);

  // Notify customer
  await sendEmail({
    to: customer.email,
    subject: 'Verification Complete!',
    body: 'Your identity verification is complete. You can now use all platform features.'
  });
}
```
{% endstep %}
{% endstepper %}

## KYB workflow (businesses)

### Overview

The KYB (Know Your Business) workflow verifies business entities through a more complex 9-step process that includes business verification and beneficial owner verification.

### How KYB differs from KYC

| Aspect | KYC (Individuals) | KYB (Businesses) |
|--------|------------------|------------------|
| **Subject** | Single person | Business entity + beneficial owners |
| **Complexity** | Moderate | High (multiple stakeholders) |
| **Documents** | Government ID | Business formation docs, tax docs, beneficial owner IDs |
| **Timeline** | 1-5 business days | 3-10 business days |
| **RFI Likelihood** | Lower | Higher (complex ownership structures) |

### Complete KYB flow

```
┌──────────────────────────────────────────────────────────────┐
│ Step 1: Business Customer Creation                          │
│ Developer creates business customer record                   │
│ Status: CREATED                                              │
└──────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────────┐
│ Step 2: Business Data Collection                            │
│ - Business information (name, type, address)                │
│ - Business documents (incorporation, tax docs)              │
│ - Beneficial owner information (25%+ ownership)             │
│ - Control person (authorized representative)                │
└──────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────────┐
│ Step 3: Validation & KYB Verification Gate                  │
│ System validates:                                            │
│ - Business information complete                              │
│ - Required business documents provided                       │
│ - At least one control person identified                     │
│ - All beneficial owners have complete data                   │
└──────────────────────────────────────────────────────────────┘
                          │
                    ┌─────┴─────┐
                    │  Pass?    │
                    └─────┬─────┘
              Yes ────────┼──────── No
                          │            │
                          │            └──► Remain CREATED
                          │
                          ▼
┌──────────────────────────────────────────────────────────────┐
│ Step 4: Business Verification Submission                    │
│ Business data sent to providers (parallel)                  │
│ Status: UNDER_REVIEW                                         │
└──────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────────┐
│ Step 5: Business Entity Review (3-7 business days)          │
│ Providers verify:                                            │
│ - Business registration and legitimacy                       │
│ - Business address and operational status                    │
│ - Tax identification                                         │
│ - Business activity and risk assessment                      │
└──────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────────┐
│ Step 6: Beneficial Owner Review                             │
│ Each beneficial owner verified individually:                │
│ - Identity verification (KYC-like process)                  │
│ - Document authentication                                    │
│ - Ownership percentage verification                          │
│ - Control and authority verification                         │
└──────────────────────────────────────────────────────────────┘
                          │
                ┌─────────┴──────────┐
                │                    │
         All Approved           RFI/Rejection
                │                    │
                ▼                    ▼
┌──────────────────────────┐  ┌──────────────────────────┐
│ Step 7A: Approval        │  │ Step 7B: RFI Required    │
│ Business credentials     │  │ Common reasons:          │
│ granted                  │  │ - Missing UBO info       │
└──────────────────────────┘  │ - Unclear ownership      │
                │             │ - Document issues        │
                │             └──────────────────────────┘
                │                         │
                │                         │ Business
                │                         │ updates data
                │                         │
                │                         ▼
                │             ┌──────────────────────────┐
                │             │ Re-submission            │
                │             │ Returns to Step 4        │
                │             └──────────────────────────┘
                │
                ▼
┌──────────────────────────────────────────────────────────────┐
│ Step 8: Cross-Verification                                   │
│ System confirms:                                             │
│ - Business entity approved                                   │
│ - All beneficial owners approved                             │
│ - Ownership structure validated                              │
└──────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────────┐
│ Step 9: Activation                                           │
│ All providers and beneficial owners approved                 │
│ Status: ACTIVE                                               │
│ Business can use platform                                    │
└──────────────────────────────────────────────────────────────┘
```

### Key KYB differences

#### Beneficial owner requirements

**Who must be verified:**
- Any individual owning 25% or more of the business
- Individuals with significant control (C-level executives, board members)
- At least one authorized representative (control person)

**For each beneficial owner:**
- Complete personal information (same as KYC)
- Government-issued ID documents
- Ownership percentage
- Role in company

**Sample request:**

{% tabs %}
{% tab title="JSON" %}
```json
{
  "beneficialOwners": [
    {
      "firstName": "John",
      "lastName": "Smith",
      "email": "john.smith@acmecorp.com",
      "dateOfBirth": "1985-06-20",
      "ownershipPercentage": 60,
      "title": "CEO & Founder",
      "documents": [
        {
          "type": "PASSPORT",
          "frontImageUrl": "https://..."
        }
      ]
    },
    {
      "firstName": "Jane",
      "lastName": "Doe",
      "email": "jane.doe@acmecorp.com",
      "dateOfBirth": "1988-09-10",
      "ownershipPercentage": 40,
      "title": "CTO & Co-Founder",
      "documents": [
        {
          "type": "DRIVERS_LICENSE",
          "frontImageUrl": "https://...",
          "backImageUrl": "https://..."
        }
      ]
    }
  ]
}
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
Missing or incomplete beneficial owner data is the number one cause of RFI for business verifications. Ensure all beneficial owners are identified and fully documented before submission.
{% endhint %}

#### Business document requirements

**Required documents:**
1. **Business formation:** Certificate of Incorporation, Articles of Organization
2. **Tax documentation:** EIN Letter, Tax Registration Certificate
3. **Proof of address:** Utility bill, lease agreement, bank statement
4. **Operating agreement:** For LLCs and partnerships (if applicable)

## Parallel verification processing

### How parallel processing works

The API submits customer data to multiple verification providers simultaneously, not sequentially. This approach:

1. **Reduces total time:** Verifications happen concurrently
2. **Improves accuracy:** Cross-validation from multiple sources
3. **Increases success rate:** One provider's rejection does not block others

### Parallel flow example

```
Customer Submission
        │
        ├──────────┬──────────┬──────────┐
        │          │          │          │
        ▼          ▼          ▼          ▼
   Provider A  Provider B  Provider C  Provider D
   (reviewing) (reviewing) (reviewing) (reviewing)
        │          │          │          │
        │          │          │          │
   (2 days)   (1 day)    (3 days)   (2 days)
        │          │          │          │
        ▼          ▼          ▼          ▼
   Approved   Approved   Approved   Approved
        │          │          │          │
        └──────────┴──────────┴──────────┘
                       │
                       ▼
            All Approved: ACTIVE
```

### Decision logic

**Customer status** is determined by:

1. **All providers approve** → `ACTIVE`
2. **Any provider requires more info** → `NEEDS_ADDITIONAL_INFORMATION`
3. **Providers still reviewing** → `UNDER_REVIEW`

{% hint style="info" %}
If one provider has a temporary issue or takes longer to review, other providers' approvals are not blocked. This improves overall success rates and reduces delays.
{% endhint %}

## Timeline expectations

### Typical verification times

| Customer Type | Typical Duration | Complex Cases | Success Rate |
|--------------|------------------|---------------|--------------|
| **KYC (Individuals)** | 1-3 business days | Up to 5 days | 85-90% |
| **KYB (Businesses)** | 3-7 business days | Up to 10 days | 75-85% |

### Timeline breakdown

**KYC timeline:**
```
Day 0:  Customer data submitted → UNDER_REVIEW
Day 1:  Automated checks complete (identity, documents, screening)
Day 2:  Manual review (if needed)
Day 3:  Decision received → ACTIVE or NEEDS_ADDITIONAL_INFORMATION
```

**KYB timeline:**
```
Day 0:  Business data submitted → UNDER_REVIEW
Day 1-2: Business entity verification
Day 2-5: Beneficial owner verification (parallel)
Day 5-7: Cross-validation and final review
Day 7:  Decision received → ACTIVE or NEEDS_ADDITIONAL_INFORMATION
```

### Factors affecting timeline

**Faster verifications:**
- Complete, high-quality data provided upfront
- Clear document images
- Simple ownership structures (for KYB)
- Straightforward cases with no risk factors

**Slower verifications:**
- Missing or incomplete data requiring RFI
- Poor document quality requiring re-upload
- Complex business ownership structures
- High-risk industries or jurisdictions
- Manual review triggered by automated checks

{% hint style="warning" %}
These are typical timelines. Actual verification duration varies based on data quality, complexity, and provider review queues. Always use webhooks to receive real-time status updates.
{% endhint %}

## Common verification scenarios

### Straightforward approval

**Flow:**
1. Customer submits complete, valid data
2. Verification gate passes
3. Providers review → All approve within 1-2 days
4. Status: `UNDER_REVIEW` → `ACTIVE`

**Developer experience:**
- Single webhook: status change to `ACTIVE`
- Total time: 1-2 business days
- No additional action required

### Request for information (RFI)

**Flow:**
1. Customer submits data
2. Provider identifies issue (e.g., blurry document image)
3. Status: `UNDER_REVIEW` → `NEEDS_ADDITIONAL_INFORMATION`
4. Customer uploads better quality image
5. System re-submits automatically
6. Provider reviews again → Approves
7. Status: `NEEDS_ADDITIONAL_INFORMATION` → `UNDER_REVIEW` → `ACTIVE`

**Developer experience:**
- Multiple webhooks: RFI notification, status changes
- Total time: 3-5 business days (including RFI resolution)
- Action required: Facilitate customer data update

**Common RFI reasons:**
- Document image quality insufficient
- Address cannot be verified
- Expired documents
- Missing beneficial owner information (KYB)
- Ownership percentages do not add to 100% (KYB)

### Partial approval with RFI

**Flow** (KYB example):
1. Business submits data with 2 beneficial owners
2. Provider A approves business entity
3. Provider B approves first beneficial owner
4. Provider C requests more info for second beneficial owner
5. Status: `UNDER_REVIEW` → `NEEDS_ADDITIONAL_INFORMATION`
6. Second beneficial owner provides additional document
7. Provider C reviews → Approves
8. Status: `NEEDS_ADDITIONAL_INFORMATION` → `UNDER_REVIEW` → `ACTIVE`

**Developer experience:**
- Incremental progress visible through multiple webhooks
- Some verifications complete while others pending
- Final activation once all requirements met

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

{% hint style="danger" %}
Once a customer is `REJECTED`, they typically cannot re-apply. Contact support if you believe a rejection was made in error or if circumstances have changed significantly.
{% endhint %}

## Monitoring verification progress

### Webhooks (recommended)

**Setup:** Configure webhook endpoint to receive real-time notifications

**Benefits:**
- Real-time status updates
- No polling required
- Efficient resource usage
- Immediate response to status changes

**Webhook example:**
```javascript
app.post('/webhooks/verification', (req, res) => {
  const { type, payload } = req.body;

  if (type === 'customer.status_changed') {
    const { customerId, newStatus, previousStatus } = payload;

    console.log(`Customer ${customerId}: ${previousStatus} → ${newStatus}`);

    // Handle status change
    switch (newStatus) {
      case 'ACTIVE':
        await handleActivation(customerId);
        break;
      case 'NEEDS_ADDITIONAL_INFORMATION':
        await handleRFI(customerId);
        break;
      case 'REJECTED':
        await handleRejection(customerId);
        break;
    }
  }

  res.sendStatus(200);
});
```

**Learn more:** [Webhook integration guide](../integration-guides/webhooks.md)

### Polling

**When to use:** Webhooks not available or as backup

**Implementation:**
```javascript
async function pollVerificationStatus(customerId) {
  const pollInterval = 30000; // 30 seconds
  const maxAttempts = 100; // 50 minutes total

  for (let attempt = 0; attempt < maxAttempts; attempt++) {
    const customer = await getCustomer(customerId);

    console.log(`Poll ${attempt + 1}: Status = ${customer.status}`);

    // Check for terminal states
    if (['ACTIVE', 'REJECTED'].includes(customer.status)) {
      return customer;
    }

    // Continue polling for in-progress states
    if (['UNDER_REVIEW', 'NEEDS_ADDITIONAL_INFORMATION'].includes(customer.status)) {
      await sleep(pollInterval);
      continue;
    }
  }

  throw new Error('Verification still in progress after polling timeout');
}
```

{% hint style="warning" %}
Polling is inefficient, subject to rate limits, and may delay your application's response to status changes. Always prefer webhooks when possible.
{% endhint %}

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
5. **Test in sandbox:** Validate your integration with test data before production

### For user experience

1. **Progress indicators:** Show verification status in your UI
2. **Clear instructions:** Explain what documents are needed and why
3. **Proactive communication:** Email customers about status changes
4. **RFI guidance:** Provide clear instructions when additional info is needed
5. **Timeline transparency:** Set expectations for 1-5 days (KYC) or 3-10 days (KYB)
6. **Support contact:** Provide easy way to contact support for verification questions
