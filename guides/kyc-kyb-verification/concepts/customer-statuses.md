# Customer Statuses

## Overview

The customer verification engine manages customer verification through a well-defined state machine with six statuses. Understanding these statuses helps you build better integrations and provide clear user experiences. Each status represents a distinct phase in the verification lifecycle, from initial creation through final approval or rejection.

Every customer has a `customerStatus` field that indicates their current verification status. This status determines what actions you can perform and what your customer can access on your platform.

**Related guides:**

* For verification process see [Verification workflow](verification-workflow.md)
* For webhook notifications see [Webhook integration](../integration-guides/webhooks.md)
* For API operations see [Customer API](../../../api-reference/customers.md)

## Status overview

The six possible statuses represent different phases of the verification lifecycle:

| Status | Description | Can Update Data? |
|-------|-------------|------------------|
| `CREATED` | Initial status, verification not started | Yes |
| `UNDER_REVIEW` | Verification in progress | No |
| `NEEDS_ADDITIONAL_INFORMATION` | Additional information required (RFI) | Yes |
| `ACTIVE` | Verification approved, customer verified | No |
| `REJECTED` | Verification permanently failed | No |
| `DEACTIVATED` | Account administratively deactivated | No |

## Status definitions

### CREATED

**What it means:** Customer profile has been created but verification has not started yet.

**Characteristics:**
- No verification process initiated
- Customer data collection phase
- All fields can be freely updated
- Default status for new customers

**Developer actions:**
- Use PATCH customers/:id to update customer data
- Add or update KYC/KYB information
- Upload identity documents
- Prepare customer for verification submission

**When it transitions:**
- Customer data is submitted and verification is automatically initiated
- Status changes to `UNDER_REVIEW` when verification begins

### UNDER_REVIEW

**What it means:** Customer verification is actively being processed.

**Characteristics:**
- Verification submission has been sent to providers
- Identity and compliance checks are in progress
- Customer data is locked to maintain submission integrity
- No action required from developer (monitoring only)

**Developer actions:**
- Monitor status via GET customers/:id
- Set up webhooks to receive status change notifications
- Display "Verification in progress" status to user
- Cannot update customer data (PATCH will return error)

**When it transitions:**
- **To `ACTIVE`:** All verification checks pass
- **To `NEEDS_ADDITIONAL_INFORMATION`:** Provider requests more information

**Typical duration:** Less than 10 minutes for individuals (1 to 3 business days if manual intervention is required), 4-7 business days for businesses

{% hint style="warning" %}
You cannot update customer data while verification is in progress. Wait for the status to transition or monitor via webhooks.
{% endhint %}

### NEEDS_ADDITIONAL_INFORMATION

**What it means:** The verification provider requires additional information, documents, or clarifications (Request for Information - RFI).

**Characteristics:**
- Verification process is paused
- Specific issues or missing data have been flagged
- Customer must take action to proceed

**Developer actions:**
- Use PATCH customers/:id to update customer data
- Upload additional documents
- Review webhook payload for specific requirements
- After updates, verification automatically re-submits

**Common reasons for RFI:**
- Missing or expired identity documents
- Unclear business ownership structure
- Insufficient address verification
- Ambiguous beneficial owner information
- Document quality issues (blurry images, partial visibility)

**Recovery process:**
1. **Receive RFI notification** via webhook or API polling
2. **Review requirements** indicated in the response
3. **Update customer data** using PATCH endpoint
4. **Verification automatically re-initiates** after update
5. **Status transitions to UNDER_REVIEW** when resubmitted

{% hint style="success" %}
This is a recoverable status. Most RFI cases can be resolved by providing the requested information.
{% endhint %}

### ACTIVE

**What it means:** Customer has successfully completed verification and is approved for platform use.

**Characteristics:**
- All verification checks passed
- Customer is fully verified
- Can perform transactions and operations
- Full platform access granted

**Developer actions:**
- Enable all platform features for the customer
- Allow transaction creation
- Grant access to payment operations
- Display verification success status
- Cannot update core customer data (contact support for changes)

**When it transitions:**
- **To `DEACTIVATED`:** Account is administratively closed
- **To `REJECTED`:** Post-approval compliance issues detected (rare)

{% hint style="success" %}
Customers in ACTIVE status have passed all compliance and identity checks. You can proceed with full platform functionality.
{% endhint %}

### REJECTED

**What it means:** Customer verification has permanently failed and cannot be approved.

**Characteristics:**
- Verification process ended negatively
- Customer cannot use platform services
- Typically due to compliance or identity verification issues
- Account closure or manual review may be required

**Developer actions:**
- Display rejection status to customer
- Provide support contact information
- Log rejection for compliance records
- Cannot update customer data
- Cannot re-initiate verification (contact support)

**Common reasons:**
- Identity documents could not be verified
- Compliance concerns or sanctions list matches
- Fraudulent information detected
- Regulatory restrictions
- Customer-requested cancellation during verification

{% hint style="warning" %}
REJECTED is typically a permanent status. Contact support if you believe this was an error or if circumstances have changed.
{% endhint %}

### DEACTIVATED

**What it means:** Customer account has been administratively deactivated.

**Characteristics:**
- Account is inactive by administrative action
- Not related to verification failure
- May be temporary or permanent
- Can potentially be reactivated (contact support)

**Developer actions:**
- Display deactivation status
- Prevent new transactions
- Allow view-only access to historical data
- Provide reactivation contact information
- Cannot update customer data
- Cannot create new transactions

**Common reasons:**
- Customer requested account closure
- Compliance hold or investigation
- Regulatory requirement
- Inactivity-based closure
- Terms of service violation

## Status transition diagram

```
┌──────────┐
│ CREATED  │ ◄─────────────────────┐
└────┬─────┘                       │
     │ Submit data                 │
     │ Verification starts         │ Data updated
     ▼                             │ Re-submit
┌──────────────┐                   │
│ UNDER_REVIEW │───────────────────┘
└──────┬───────┘
       │
       ├─────► All checks pass
       │       ▼
       │    ┌────────┐
       │    │ ACTIVE │
       │    └────┬───┘
       │         │
       │         ├──► Admin action ──► DEACTIVATED
       │         └──► Compliance issue ──► REJECTED
       │
       └─────► Need more info
               ▼
         ┌─────────────────────┐
         │ NEEDS_ADDITIONAL    │
         │    _INFORMATION     │
         └──────┬──────────────┘
                │
                ├──► Update data ──► Returns to UNDER_REVIEW
                └──► Irrecoverable ──► REJECTED
```

### Transition summary

| From | To | Trigger | Automatic? |
|------|-----|---------|-----------|
| CREATED | UNDER_REVIEW | Verification submission | Yes |
| UNDER_REVIEW | ACTIVE | All checks passed | Yes |
| UNDER_REVIEW | NEEDS_ADDITIONAL_INFORMATION | Provider requests more info | Yes |
| NEEDS_ADDITIONAL_INFORMATION | UNDER_REVIEW | Customer updates data | Yes |
| NEEDS_ADDITIONAL_INFORMATION | REJECTED | Irrecoverable failure | Manual |
| ACTIVE | DEACTIVATED | Administrative action | Manual |
| ACTIVE | REJECTED | Post-approval issues | Manual |

## Testing customer statuses

### Test checklist

Test your integration handles all statuses:

- CREATED - Show profile completion UI
- UNDER_REVIEW - Show waiting/progress indicator
- NEEDS_ADDITIONAL_INFORMATION - Show RFI resolution flow
- ACTIVE - Enable full platform features
- REJECTED - Show support contact and disable features
- DEACTIVATED - Show reactivation contact and disable transactions

## Frequently asked questions

### How long does verification take?

- **Individuals (KYC):** Typically under 10 minutes
- **Businesses (KYB):** Typically 4-7 business days
- Complex cases may take longer

### Can I expedite verification?

Contact your account manager to discuss expedited processing options for your use case.

### What happens if verification fails?

If verification transitions to `NEEDS_ADDITIONAL_INFORMATION`, you can provide the requested information and resubmit. If it transitions to `REJECTED`, contact support.

### Can I update customer data after approval?

Once a customer is `ACTIVE`, profile data cannot be updated via the API. Contact support for approved customer data changes.

### How do I handle RFI situations?

1. Monitor for `NEEDS_ADDITIONAL_INFORMATION` status
2. Review webhook payload or API response for specific requirements
3. Update customer data with requested information
4. System automatically re-submits for verification

### What is the difference between REJECTED and DEACTIVATED?

- `REJECTED`: Verification failed due to identity or compliance issues
- `DEACTIVATED`: Administrative account closure unrelated to verification failure
