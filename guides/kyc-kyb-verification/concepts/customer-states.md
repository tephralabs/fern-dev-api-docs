# Customer states

## Overview

The customer verification API manages customer verification through a well-defined state machine with six states. Understanding these states helps you build better integrations and provide clear user experiences. Each state represents a distinct phase in the verification lifecycle, from initial creation through final approval or rejection.

Every customer has a `status` field that indicates their current verification state. This status determines what actions you can perform and what your customer can access on your platform.

**Related guides:**

* For verification process see [Verification workflow](verification-workflow.md)
* For webhook notifications see [Webhook integration](../integration-guides/webhooks.md)
* For API operations see [<!-- PHASE6_API_REFERENCE: Customer API -->](../../../api-reference/customers.md)

## State overview

Every customer has a `status` field that indicates their current verification state:

```json
{
  "id": "cus_1a2b3c4d5e6f",
  "status": "UNDER_REVIEW",
  "type": "INDIVIDUAL"
}
```

The six possible states represent different phases of the verification lifecycle:

| State | Description | Can Update Data? |
|-------|-------------|------------------|
| `CREATED` | Initial state, verification not started | Yes |
| `UNDER_REVIEW` | Verification in progress | No |
| `NEEDS_ADDITIONAL_INFORMATION` | Additional information required (RFI) | Yes |
| `ACTIVE` | Verification approved, customer verified | No |
| `REJECTED` | Verification permanently failed | No |
| `DEACTIVATED` | Account administratively deactivated | No |

## State definitions

### CREATED

**What it means:** Customer profile has been created but verification has not started yet.

**Characteristics:**
- No verification process initiated
- Customer data collection phase
- All fields can be freely updated
- Default state for new customers

**Developer actions:**
- Use <!-- PHASE6_API_REFERENCE: PATCH /api/v1/customers/:id --> to update customer data
- Add or update KYC/KYB information
- Upload identity documents
- Prepare customer for verification submission

**When it transitions:**
- Customer data is submitted and verification is automatically initiated
- State changes to `UNDER_REVIEW` when verification begins

**Example use case:**
```javascript
// Create customer in CREATED state
const customer = await createCustomer({
  type: 'INDIVIDUAL',
  email: 'user@example.com',
  firstName: 'John',
  lastName: 'Doe'
});

// Status: CREATED - can update freely
await updateCustomer(customer.id, {
  dateOfBirth: '1990-01-01',
  address: { /* ... */ }
});
```

### UNDER_REVIEW

**What it means:** Customer verification is actively being processed.

**Characteristics:**
- Verification submission has been sent to providers
- Identity and compliance checks are in progress
- Customer data is locked to maintain submission integrity
- No action required from developer (monitoring only)

**Developer actions:**
- Monitor status via <!-- PHASE6_API_REFERENCE: GET /api/v1/customers/:id -->
- Set up webhooks to receive status change notifications
- Display "Verification in progress" status to user
- Cannot update customer data (PATCH will return error)

**When it transitions:**
- **To `ACTIVE`:** All verification checks pass
- **To `NEEDS_ADDITIONAL_INFORMATION`:** Provider requests more information

**Typical duration:** 1-3 business days for individuals, 3-7 business days for businesses

**Example:**
```javascript
// Customer is under review
const customer = await getCustomer('cus_1a2b3c4d5e6f');
console.log(customer.status); // "UNDER_REVIEW"

// Attempting to update will fail
try {
  await updateCustomer('cus_1a2b3c4d5e6f', {
    dateOfBirth: '1991-01-01'
  });
} catch (error) {
  // Error: Cannot update customer in UNDER_REVIEW status
}
```

{% hint style="warning" %}
You cannot update customer data while verification is in progress. Wait for the state to transition or monitor via webhooks.
{% endhint %}

### NEEDS_ADDITIONAL_INFORMATION

**What it means:** The verification provider requires additional information, documents, or clarifications (Request for Information - RFI).

**Characteristics:**
- Verification process is paused
- Specific issues or missing data have been flagged
- Customer must take action to proceed
- Common in complex business verifications

**Developer actions:**
- Use <!-- PHASE6_API_REFERENCE: PATCH /api/v1/customers/:id --> to update customer data
- Upload additional documents
- Provide clarifications on flagged fields
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
5. **State transitions to UNDER_REVIEW** when resubmitted

**Example recovery flow:**
```javascript
// Customer needs additional information
const customer = await getCustomer('cus_1a2b3c4d5e6f');
console.log(customer.status); // "NEEDS_ADDITIONAL_INFORMATION"

// Update with missing information
await updateCustomer('cus_1a2b3c4d5e6f', {
  address: {
    street: '456 Updated Street',
    city: 'San Francisco',
    state: 'CA',
    postalCode: '94103',
    country: 'US'
  },
  documents: [
    {
      type: 'DRIVERS_LICENSE',
      frontImageBase64: '...',
      backImageBase64: '...'
    }
  ]
});

// System automatically re-evaluates and re-submits
// Customer transitions back to UNDER_REVIEW
```

{% hint style="success" %}
This is a recoverable state. Most RFI cases can be resolved by providing the requested information.
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

**Example:**
```javascript
const customer = await getCustomer('cus_1a2b3c4d5e6f');

if (customer.status === 'ACTIVE') {
  // Customer is verified - enable full platform access
  await enableTransactions(customer.id);
  await notifyCustomer('Verification complete! Your account is active.');
}
```

{% hint style="success" %}
Customers in ACTIVE state have passed all compliance and identity checks. You can proceed with full platform functionality.
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

**Example:**
```javascript
const customer = await getCustomer('cus_1a2b3c4d5e6f');

if (customer.status === 'REJECTED') {
  await notifyCustomer(
    'We were unable to verify your account. Please contact support for assistance.'
  );
  await logComplianceEvent('customer_rejected', customer.id);
}
```

{% hint style="warning" %}
REJECTED is typically a permanent state. Contact support if you believe this was an error or if circumstances have changed.
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

**Example:**
```javascript
const customer = await getCustomer('cus_1a2b3c4d5e6f');

if (customer.status === 'DEACTIVATED') {
  await disableTransactions(customer.id);
  await notifyCustomer(
    'Your account is currently deactivated. Contact support to reactivate.'
  );
}
```

## State transition diagram

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

## Developer integration patterns

### Polling for status changes

If you're not using webhooks, poll the customer status endpoint:

```javascript
async function pollCustomerStatus(customerId, maxAttempts = 60) {
  for (let i = 0; i < maxAttempts; i++) {
    const customer = await getCustomer(customerId);

    if (customer.status === 'ACTIVE') {
      console.log('Verification complete!');
      return customer;
    }

    if (customer.status === 'NEEDS_ADDITIONAL_INFORMATION') {
      console.log('Additional information required');
      return customer;
    }

    if (['REJECTED', 'DEACTIVATED'].includes(customer.status)) {
      console.log('Verification ended:', customer.status);
      return customer;
    }

    // Still UNDER_REVIEW - wait and retry
    await new Promise(resolve => setTimeout(resolve, 30000)); // 30 seconds
  }
}
```

{% hint style="warning" %}
Polling is inefficient and subject to rate limits. We strongly recommend using webhooks instead.
{% endhint %}

### Webhook-based status monitoring (recommended)

```javascript
// Webhook handler for customer status changes
app.post('/webhooks/customer-status', async (req, res) => {
  const { customerId, newStatus, previousStatus } = req.body;

  console.log(`Customer ${customerId}: ${previousStatus} → ${newStatus}`);

  switch (newStatus) {
    case 'ACTIVE':
      await enableCustomerFeatures(customerId);
      await sendEmail(customerId, 'Verification complete!');
      break;

    case 'NEEDS_ADDITIONAL_INFORMATION':
      await sendEmail(customerId, 'Additional information required');
      await notifySupport(customerId, 'Customer needs assistance');
      break;

    case 'REJECTED':
      await sendEmail(customerId, 'Verification unsuccessful');
      await logComplianceEvent('customer_rejected', customerId);
      break;

    case 'UNDER_REVIEW':
      await sendEmail(customerId, 'Verification in progress');
      break;
  }

  res.sendStatus(200);
});
```

**Learn more:** [Webhook integration guide](../integration-guides/webhooks.md)

## State-based business logic

### Enabling features by state

```javascript
function getAvailableFeatures(customerStatus) {
  const features = {
    viewProfile: ['CREATED', 'UNDER_REVIEW', 'NEEDS_ADDITIONAL_INFORMATION', 'ACTIVE', 'DEACTIVATED'],
    updateProfile: ['CREATED', 'NEEDS_ADDITIONAL_INFORMATION'],
    createTransaction: ['ACTIVE'],
    viewTransactionHistory: ['ACTIVE', 'DEACTIVATED'],
    accessAPIs: ['ACTIVE']
  };

  return Object.keys(features).filter(feature =>
    features[feature].includes(customerStatus)
  );
}

// Usage
const customer = await getCustomer('cus_1a2b3c4d5e6f');
const availableFeatures = getAvailableFeatures(customer.status);

if (availableFeatures.includes('createTransaction')) {
  // Show transaction creation UI
}
```

### User experience messages

```javascript
function getCustomerMessage(status) {
  const messages = {
    CREATED: {
      title: 'Complete your profile',
      message: 'Add your information to begin verification.',
      action: 'Complete Profile'
    },
    UNDER_REVIEW: {
      title: 'Verification in progress',
      message: 'We are reviewing your information. This typically takes 1-3 business days.',
      action: 'Check Status'
    },
    NEEDS_ADDITIONAL_INFORMATION: {
      title: 'Action required',
      message: 'We need additional information to complete your verification.',
      action: 'Provide Information'
    },
    ACTIVE: {
      title: 'Account active',
      message: 'Your account is verified and ready to use!',
      action: 'Get Started'
    },
    REJECTED: {
      title: 'Verification unsuccessful',
      message: 'We were unable to verify your account. Contact support for assistance.',
      action: 'Contact Support'
    },
    DEACTIVATED: {
      title: 'Account deactivated',
      message: 'Your account is currently deactivated. Contact support to reactivate.',
      action: 'Contact Support'
    }
  };

  return messages[status];
}
```

## Testing customer states

### Sandbox environment

In sandbox mode, you can simulate different customer states:

```javascript
// Create customer
const customer = await createCustomer({
  type: 'INDIVIDUAL',
  email: 'test@example.com',
  firstName: 'Test',
  lastName: 'User'
});
// Status: CREATED

// Simulate UNDER_REVIEW (automatic after data submission)
await updateCustomer(customer.id, {
  dateOfBirth: '1990-01-01',
  address: { /* ... */ }
});
// Status: UNDER_REVIEW (in sandbox, transitions immediately)

// In sandbox, you can force specific states for testing
await sandboxSetCustomerStatus(customer.id, 'ACTIVE');
// Status: ACTIVE

await sandboxSetCustomerStatus(customer.id, 'NEEDS_ADDITIONAL_INFORMATION');
// Status: NEEDS_ADDITIONAL_INFORMATION
```

### Test checklist

Test your integration handles all states:

- CREATED - Show profile completion UI
- UNDER_REVIEW - Show waiting/progress indicator
- NEEDS_ADDITIONAL_INFORMATION - Show RFI resolution flow
- ACTIVE - Enable full platform features
- REJECTED - Show support contact and disable features
- DEACTIVATED - Show reactivation contact and disable transactions

## Frequently asked questions

### How long does verification take?

- **Individuals (KYC):** Typically 1-3 business days
- **Businesses (KYB):** Typically 3-7 business days
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
