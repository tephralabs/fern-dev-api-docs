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
* For webhook setup see [Webhook integration guide](webhooks.md)
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
This guide uses placeholder URLs like `api.example.com`. Replace these with your actual API base URLs when implementing.
{% endhint %}

---

## Verification timeline

Understanding the typical timeline helps set proper expectations:

- **Data collection**: Immediate (user fills form in your application)
- **Verification processing**: 1-5 business days after submission
- **Additional information requests**: May add 2-3 days if documents need clarification
- **Total time**: 1-7 business days from initial submission to approval

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

<!-- PHASE6_API_UPDATE: ENDPOINT - Verify customer creation endpoint -->
**Endpoint**: `POST /api/v1/customers`
<!-- /PHASE6_API_UPDATE -->

{% tabs %}
{% tab title="JavaScript" %}
<!-- PHASE6_API_UPDATE: BASE_URL - Verify production API base URL -->
```javascript
async function createIndividualCustomer(email, firstName, lastName) {
  const response = await fetch('https://api.example.com/api/v1/customers', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      type: 'INDIVIDUAL',
      email: email,
      firstName: firstName,
      lastName: lastName
    })
  });

  if (!response.ok) {
    const error = await response.json();
    throw new Error(`Failed to create customer: ${error.message}`);
  }

  const customer = await response.json();
  console.log('Customer created:', customer.id);
  console.log('Status:', customer.status); // "CREATED"

  return customer;
}

// Usage
const customer = await createIndividualCustomer(
  'jane.smith@example.com',
  'Jane',
  'Smith'
);
```
<!-- /PHASE6_API_UPDATE -->
{% endtab %}

{% tab title="Python" %}
<!-- PHASE6_API_UPDATE: BASE_URL - Verify production API base URL -->
```python
import requests
import os

def create_individual_customer(email, first_name, last_name):
    response = requests.post(
        'https://api.example.com/api/v1/customers',
        headers={
            'Authorization': f'Bearer {os.environ["API_KEY"]}',
            'Content-Type': 'application/json'
        },
        json={
            'type': 'INDIVIDUAL',
            'email': email,
            'firstName': first_name,
            'lastName': last_name
        }
    )

    response.raise_for_status()
    customer = response.json()

    print(f"Customer created: {customer['id']}")
    print(f"Status: {customer['status']}")  # "CREATED"

    return customer

# Usage
customer = create_individual_customer(
    'jane.smith@example.com',
    'Jane',
    'Smith'
)
```
<!-- /PHASE6_API_UPDATE -->
{% endtab %}

{% tab title="Go" %}
<!-- PHASE6_API_UPDATE: BASE_URL - Verify production API base URL -->
```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io"
    "net/http"
    "os"
)

type CreateCustomerRequest struct {
    Type      string `json:"type"`
    Email     string `json:"email"`
    FirstName string `json:"firstName"`
    LastName  string `json:"lastName"`
}

type Customer struct {
    ID     string `json:"id"`
    Status string `json:"status"`
    // Add other fields as needed
}

func createIndividualCustomer(email, firstName, lastName string) (*Customer, error) {
    reqBody := CreateCustomerRequest{
        Type:      "INDIVIDUAL",
        Email:     email,
        FirstName: firstName,
        LastName:  lastName,
    }

    jsonData, err := json.Marshal(reqBody)
    if err != nil {
        return nil, err
    }

    req, err := http.NewRequest("POST",
        "https://api.example.com/api/v1/customers",
        bytes.NewBuffer(jsonData))
    if err != nil {
        return nil, err
    }

    req.Header.Set("Authorization", "Bearer "+os.Getenv("API_KEY"))
    req.Header.Set("Content-Type", "application/json")

    client := &http.Client{}
    resp, err := client.Do(req)
    if err != nil {
        return nil, err
    }
    defer resp.Body.Close()

    if resp.StatusCode != http.StatusCreated {
        body, _ := io.ReadAll(resp.Body)
        return nil, fmt.Errorf("failed to create customer: %s", body)
    }

    var customer Customer
    if err := json.NewDecoder(resp.Body).Decode(&customer); err != nil {
        return nil, err
    }

    fmt.Printf("Customer created: %s\n", customer.ID)
    fmt.Printf("Status: %s\n", customer.Status)

    return &customer, nil
}

// Usage
func main() {
    customer, err := createIndividualCustomer(
        "jane.smith@example.com",
        "Jane",
        "Smith",
    )
    if err != nil {
        panic(err)
    }
}
```
<!-- /PHASE6_API_UPDATE -->
{% endtab %}
{% endtabs %}

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
  "verificationLink": "https://app.example.com/verify-customer/abc123...",
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

<!-- PHASE6_API_UPDATE: ENDPOINT - Verify customer update endpoint -->
**Endpoint**: `PATCH /api/v1/customers/:customerId`
<!-- /PHASE6_API_UPDATE -->

{% tabs %}
{% tab title="JavaScript" %}
<!-- PHASE6_API_UPDATE: BASE_URL - Verify production API base URL -->
```javascript
async function submitKycData(customerId, kycData) {
  const response = await fetch(
    `https://api.example.com/api/v1/customers/${customerId}`,
    {
      method: 'PATCH',
      headers: {
        'Authorization': `Bearer ${process.env.API_KEY}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ kycData })
    }
  );

  if (!response.ok) {
    const error = await response.json();

    // Handle state-based restrictions
    if (error.error === 'PATCH_NOT_ALLOWED') {
      console.error('Cannot update: verification in progress');
      console.error('Current status:', error.customerStatus);
      throw new Error('Customer data locked during verification');
    }

    throw new Error(`Failed to submit KYC data: ${error.message}`);
  }

  const customer = await response.json();
  console.log('KYC data submitted');
  console.log('New status:', customer.status); // Should transition to "UNDER_REVIEW"

  return customer;
}

// Usage - complete KYC data example
const kycData = {
  legalFirstName: 'Jane',
  legalLastName: 'Smith',
  phoneNumber: '+14155551234',
  dateOfBirth: '1990-05-15',
  address: {
    addressLine1: '123 Main Street',
    addressLine2: 'Apt 4B',
    city: 'San Francisco',
    state: 'California',
    stateCode: 'CA',
    postalCode: '94102',
    country: 'US',
    locale: 'en-US'
  },
  nationalIdType: 'SSN',
  nationalIdNumber: '123-45-6789',
  nationalIdIssuingCountry: 'US',
  nationality: 'US',
  employmentStatus: 'EMPLOYED',
  mostRecentOccupation: 'Software Engineer',
  sourceOfFunds: 'EMPLOYMENT',
  accountPurpose: 'PERSONAL_USE',
  expectedMonthlyPaymentsUsd: 'UNDER_10K',
  isIntermediary: false,
  documents: [
    {
      type: 'PASSPORT',
      number: 'P12345678',
      issuingCountry: 'US',
      expirationDate: '2030-12-31',
      frontImageUrl: 'https://your-storage.com/passport-front.jpg'
    }
  ]
};

const updatedCustomer = await submitKycData('customer_abc123', kycData);
```
<!-- /PHASE6_API_UPDATE -->
{% endtab %}

{% tab title="Python" %}
<!-- PHASE6_API_UPDATE: BASE_URL - Verify production API base URL -->
```python
def submit_kyc_data(customer_id, kyc_data):
    response = requests.patch(
        f'https://api.example.com/api/v1/customers/{customer_id}',
        headers={
            'Authorization': f'Bearer {os.environ["API_KEY"]}',
            'Content-Type': 'application/json'
        },
        json={'kycData': kyc_data}
    )

    if not response.ok:
        error = response.json()

        # Handle state-based restrictions
        if error.get('error') == 'PATCH_NOT_ALLOWED':
            print('Cannot update: verification in progress')
            print(f'Current status: {error.get("customerStatus")}')
            raise ValueError('Customer data locked during verification')

        raise Exception(f'Failed to submit KYC data: {error["message"]}')

    customer = response.json()
    print('KYC data submitted')
    print(f'New status: {customer["status"]}')  # Should be "UNDER_REVIEW"

    return customer

# Usage - complete KYC data example
kyc_data = {
    'legalFirstName': 'Jane',
    'legalLastName': 'Smith',
    'phoneNumber': '+14155551234',
    'dateOfBirth': '1990-05-15',
    'address': {
        'addressLine1': '123 Main Street',
        'addressLine2': 'Apt 4B',
        'city': 'San Francisco',
        'state': 'California',
        'stateCode': 'CA',
        'postalCode': '94102',
        'country': 'US',
        'locale': 'en-US'
    },
    'nationalIdType': 'SSN',
    'nationalIdNumber': '123-45-6789',
    'nationalIdIssuingCountry': 'US',
    'nationality': 'US',
    'employmentStatus': 'EMPLOYED',
    'mostRecentOccupation': 'Software Engineer',
    'sourceOfFunds': 'EMPLOYMENT',
    'accountPurpose': 'PERSONAL_USE',
    'expectedMonthlyPaymentsUsd': 'UNDER_10K',
    'isIntermediary': False,
    'documents': [
        {
            'type': 'PASSPORT',
            'number': 'P12345678',
            'issuingCountry': 'US',
            'expirationDate': '2030-12-31',
            'frontImageUrl': 'https://your-storage.com/passport-front.jpg'
        }
    ]
}

updated_customer = submit_kyc_data('customer_abc123', kyc_data)
```
<!-- /PHASE6_API_UPDATE -->
{% endtab %}

{% tab title="Go" %}
<!-- PHASE6_API_UPDATE: BASE_URL - Verify production API base URL -->
```go
type Address struct {
    AddressLine1 string `json:"addressLine1"`
    AddressLine2 string `json:"addressLine2,omitempty"`
    City         string `json:"city"`
    State        string `json:"state"`
    StateCode    string `json:"stateCode"`
    PostalCode   string `json:"postalCode"`
    Country      string `json:"country"`
    Locale       string `json:"locale"`
}

type KycDocument struct {
    Type           string `json:"type"`
    Number         string `json:"number"`
    IssuingCountry string `json:"issuingCountry"`
    ExpirationDate string `json:"expirationDate"`
    FrontImageUrl  string `json:"frontImageUrl"`
}

type KycData struct {
    LegalFirstName             string        `json:"legalFirstName"`
    LegalLastName              string        `json:"legalLastName"`
    PhoneNumber                string        `json:"phoneNumber"`
    DateOfBirth                string        `json:"dateOfBirth"`
    Address                    Address       `json:"address"`
    NationalIdType             string        `json:"nationalIdType"`
    NationalIdNumber           string        `json:"nationalIdNumber"`
    NationalIdIssuingCountry   string        `json:"nationalIdIssuingCountry"`
    Nationality                string        `json:"nationality"`
    EmploymentStatus           string        `json:"employmentStatus"`
    MostRecentOccupation       string        `json:"mostRecentOccupation"`
    SourceOfFunds              string        `json:"sourceOfFunds"`
    AccountPurpose             string        `json:"accountPurpose"`
    ExpectedMonthlyPaymentsUsd string        `json:"expectedMonthlyPaymentsUsd"`
    IsIntermediary             bool          `json:"isIntermediary"`
    Documents                  []KycDocument `json:"documents"`
}

type PatchRequest struct {
    KycData KycData `json:"kycData"`
}

func submitKycData(customerID string, kycData KycData) (*Customer, error) {
    reqBody := PatchRequest{KycData: kycData}

    jsonData, err := json.Marshal(reqBody)
    if err != nil {
        return nil, err
    }

    url := fmt.Sprintf("https://api.example.com/api/v1/customers/%s", customerID)
    req, err := http.NewRequest("PATCH", url, bytes.NewBuffer(jsonData))
    if err != nil {
        return nil, err
    }

    req.Header.Set("Authorization", "Bearer "+os.Getenv("API_KEY"))
    req.Header.Set("Content-Type", "application/json")

    client := &http.Client{}
    resp, err := client.Do(req)
    if err != nil {
        return nil, err
    }
    defer resp.Body.Close()

    if resp.StatusCode != http.StatusOK {
        body, _ := io.ReadAll(resp.Body)
        return nil, fmt.Errorf("failed to submit KYC data: %s", body)
    }

    var customer Customer
    if err := json.NewDecoder(resp.Body).Decode(&customer); err != nil {
        return nil, err
    }

    fmt.Printf("KYC data submitted. New status: %s\n", customer.Status)
    return &customer, nil
}
```
<!-- /PHASE6_API_UPDATE -->
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
Customer status will automatically transition to `UNDER_REVIEW` upon successful data submission. You cannot modify data while status is `UNDER_REVIEW` (verification in progress). Always handle `PATCH_NOT_ALLOWED` errors gracefully.
{% endhint %}
{% endstep %}

{% step %}
### Monitor verification status

Monitor the customer's verification status using webhooks (recommended) or polling.

#### Option A: Webhooks (recommended)

Set up a webhook endpoint to receive real-time status updates:

<!-- PHASE6_API_UPDATE: WEBHOOK_EVENTS - Verify webhook event structure -->
```javascript
// Express.js webhook handler example
app.post('/webhooks/customer-status', async (req, res) => {
  const event = req.body;

  // Verify webhook signature (see Webhook guide)
  // ... signature verification code ...

  if (event.type === 'customer.state_changed') {
    const { customerId, oldStatus, newStatus } = event.payload;

    console.log(`Customer ${customerId} status changed: ${oldStatus} → ${newStatus}`);

    switch (newStatus) {
      case 'ACTIVE':
        console.log('Verification approved! Customer can now use services.');
        await enableCustomerFeatures(customerId);
        await sendApprovalEmail(customerId);
        break;

      case 'NEEDS_ADDITIONAL_INFORMATION':
        console.log('Additional information required');
        await sendRFINotification(customerId, event.payload.requiredDocuments);
        break;

      case 'REJECTED':
        console.log('Verification rejected');
        await sendRejectionNotification(customerId, event.payload.reason);
        break;

      case 'UNDER_REVIEW':
        console.log('Verification in progress');
        await sendProgressUpdate(customerId);
        break;
    }
  }

  res.status(200).json({ received: true });
});
```
<!-- /PHASE6_API_UPDATE -->

See the complete [Webhook integration guide](webhooks.md) for setup instructions.

#### Option B: Polling

If webhooks aren't available, poll the customer status periodically:

```javascript
async function pollCustomerStatus(customerId, maxAttempts = 50) {
  const pollInterval = 30000; // 30 seconds

  for (let attempt = 0; attempt < maxAttempts; attempt++) {
    const response = await fetch(
      `https://api.example.com/api/v1/customers/${customerId}`,
      {
        headers: {
          'Authorization': `Bearer ${process.env.API_KEY}`
        }
      }
    );

    const customer = await response.json();
    console.log(`Status check ${attempt + 1}: ${customer.status}`);

    // Terminal states - stop polling
    if (['ACTIVE', 'REJECTED'].includes(customer.status)) {
      console.log('Verification complete:', customer.status);
      return customer;
    }

    // Continue polling for in-progress states
    if (['UNDER_REVIEW', 'NEEDS_ADDITIONAL_INFORMATION'].includes(customer.status)) {
      await new Promise(resolve => setTimeout(resolve, pollInterval));
      continue;
    }

    // Unexpected state
    console.warn('Unexpected status:', customer.status);
    return customer;
  }

  throw new Error('Polling timeout: verification still in progress');
}
```

{% hint style="warning" %}
Polling is less efficient than webhooks and may delay your application's response to status changes. Use webhooks when possible.
{% endhint %}
{% endstep %}

{% step %}
### Handle requests for additional information

When verification status becomes `NEEDS_ADDITIONAL_INFORMATION`, the verification provider needs additional documents or clarifications.

**What to do:**

1. Retrieve customer details to see what's needed:

```javascript
async function handleRFI(customerId) {
  const response = await fetch(
    `https://api.example.com/api/v1/customers/${customerId}`,
    {
      headers: {
        'Authorization': `Bearer ${process.env.API_KEY}`
      }
    }
  );

  const customer = await response.json();

  if (customer.status === 'NEEDS_ADDITIONAL_INFORMATION') {
    console.log('Additional information required:');

    // Check customer object for guidance on what's needed
    // (specific fields depend on verification provider feedback)

    // Notify customer via email/app notification
    await notifyCustomer(customerId, {
      message: 'Please provide additional documentation for verification',
      requiredActions: [
        'Upload clear photo of government-issued ID',
        'Verify residential address matches ID',
        'Provide proof of address document'
      ]
    });
  }
}
```

2. Collect additional data from customer

3. Resubmit updated data using PATCH:

```javascript
// Customer can update data when status is NEEDS_ADDITIONAL_INFORMATION
const updatedKycData = {
  documents: [
    {
      type: 'DRIVERS_LICENSE',
      number: 'D1234567',
      issuingCountry: 'US',
      expirationDate: '2028-05-15',
      frontImageUrl: 'https://your-storage.com/license-front.jpg',
      backImageUrl: 'https://your-storage.com/license-back.jpg'
    },
    {
      type: 'UTILITY_BILL',
      issueDate: '2025-10-01',
      frontImageUrl: 'https://your-storage.com/utility-bill.jpg'
    }
  ]
};

const updatedCustomer = await submitKycData(customerId, updatedKycData);
console.log('Updated data submitted. New status:', updatedCustomer.status);
// Status should return to UNDER_REVIEW for re-evaluation
```

{% hint style="info" %}
Request for Additional Information (RFI) typically occurs when document quality is poor or information doesn't match across sources. Providing clear instructions to customers upfront reduces RFI rates.
{% endhint %}
{% endstep %}

{% step %}
### Verification complete

When verification is approved, customer status becomes `ACTIVE` and the customer can access your platform's services.

```javascript
async function activateCustomer(customerId) {
  // Verify customer is active
  const response = await fetch(
    `https://api.example.com/api/v1/customers/${customerId}`,
    {
      headers: {
        'Authorization': `Bearer ${process.env.API_KEY}`
      }
    }
  );

  const customer = await response.json();

  if (customer.status === 'ACTIVE') {
    console.log('Customer verification complete!');

    // Enable platform features for this customer
    await enablePaymentAccounts(customerId);
    await grantAPIAccess(customerId);

    // Notify customer
    await sendEmail({
      to: customer.email,
      subject: 'Verification Complete - Account Activated',
      body: 'Your identity verification is complete. You can now access all platform features.'
    });

    return { success: true, customer };
  } else {
    return { success: false, status: customer.status };
  }
}
```

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
| `addressLine1` | string | Yes | Street address | "123 Main Street" |
| `addressLine2` | string | No | Apartment, suite, unit | "Apt 4B" |
| `city` | string | Yes | City name | "San Francisco" |
| `state` | string | Yes | State/province name | "California" |
| `stateCode` | string | Yes | 2-letter state code | "CA" |
| `postalCode` | string | Yes | Zip/postal code | "94102" |
| `country` | string | Yes | ISO 3166-1 alpha-2 code | "US" |
| `locale` | string | Yes | Language and region | "en-US" |

### National identification

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

**Accepted ID types:**
* Passport
* Driver's License
* National ID Card
* Residence Permit
* State ID Card

**Additional documents** (recommended to speed up verification):
* Proof of Address (utility bill, bank statement, lease agreement)
* Additional ID for cross-verification

### Document format

<!-- PHASE6_API_UPDATE: REQUEST_PAYLOAD - Validate document structure -->
```javascript
{
  type: 'PASSPORT',              // Document type (see types above)
  number: 'P12345678',           // Document number
  issuingCountry: 'US',          // ISO 3166-1 alpha-2 code
  expirationDate: '2030-12-31',  // ISO 8601 date
  frontImageUrl: 'https://...',  // URL to front image
  backImageUrl: 'https://...'    // URL to back image (if applicable)
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
* **Resolution**: Minimum 300 DPI recommended
* **Color**: Full color (no black and white)

**Quality guidelines:**
* Clear and legible - All text must be readable
* Well-lit - No shadows or glare
* Complete - All four corners visible
* Unobstructed - No fingers or objects covering document
* Current - Not expired
* Avoid blurry, dark, cropped, or screenshot images

### Image upload process

1. Customer uploads image to your storage (S3, Cloudinary, etc.)
2. Generate secure URL (temporary or permanent with access control)
3. Include URL in KYC data when submitting via PATCH

```javascript
async function uploadDocumentImage(file) {
  // Upload to your storage service
  const formData = new FormData();
  formData.append('file', file);

  const uploadResponse = await fetch('https://your-storage-api.com/upload', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${YOUR_STORAGE_API_KEY}`
    },
    body: formData
  });

  const { url } = await uploadResponse.json();
  return url; // Return URL to include in KYC data
}

// Usage
const passportFrontUrl = await uploadDocumentImage(passportFrontFile);

const kycData = {
  // ... other fields
  documents: [
    {
      type: 'PASSPORT',
      number: 'P12345678',
      issuingCountry: 'US',
      expirationDate: '2030-12-31',
      frontImageUrl: passportFrontUrl
    }
  ]
};
```

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

```javascript
async function safePatchKycData(customerId, kycData) {
  // Check current status first
  const customer = await getCustomer(customerId);

  const allowedStatuses = ['CREATED', 'NEEDS_ADDITIONAL_INFORMATION', 'REJECTED'];

  if (!allowedStatuses.includes(customer.status)) {
    throw new Error(
      `Cannot update customer: status is ${customer.status}. ` +
      `Updates only allowed in: ${allowedStatuses.join(', ')}`
    );
  }

  // Proceed with PATCH
  return await submitKycData(customerId, kycData);
}
```

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

```javascript
function validateKycData(kycData) {
  const errors = [];

  // Phone number validation (E.164 format)
  if (kycData.phoneNumber && !/^\+[1-9]\d{1,14}$/.test(kycData.phoneNumber)) {
    errors.push('Phone number must be in E.164 format (e.g., +14155551234)');
  }

  // Date of birth validation (ISO 8601 YYYY-MM-DD)
  if (kycData.dateOfBirth && !/^\d{4}-\d{2}-\d{2}$/.test(kycData.dateOfBirth)) {
    errors.push('Date of birth must be in YYYY-MM-DD format');
  }

  // Age validation (must be 18+)
  if (kycData.dateOfBirth) {
    const age = calculateAge(kycData.dateOfBirth);
    if (age < 18) {
      errors.push('Customer must be at least 18 years old');
    }
  }

  // Country code validation (ISO 3166-1 alpha-2)
  if (kycData.address?.country && kycData.address.country.length !== 2) {
    errors.push('Country must be ISO 3166-1 alpha-2 code (e.g., "US")');
  }

  return errors;
}

// Usage
const errors = validateKycData(kycData);
if (errors.length > 0) {
  console.error('Validation errors:', errors);
  return; // Don't submit
}

await submitKycData(customerId, kycData);
```

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

```javascript
async function makeAuthenticatedRequest(url, options) {
  let response = await fetch(url, options);

  // If unauthorized, try refreshing token
  if (response.status === 401) {
    console.log('Token expired, refreshing...');
    const newToken = await refreshAuthToken();

    // Retry with new token
    options.headers['Authorization'] = `Bearer ${newToken}`;
    response = await fetch(url, options);
  }

  return response;
}
```

---

## Testing in sandbox

### Sandbox environment

<!-- PHASE6_API_UPDATE: BASE_URL - Verify sandbox API base URL -->
**Base URL**: `https://api.sandbox.example.com/api/v1`
<!-- /PHASE6_API_UPDATE -->

The sandbox environment simulates verification flows for testing without processing real customer data.

### Test scenarios

#### Scenario 1: Successful verification

```javascript
// Create customer and submit complete, valid KYC data
const customer = await createIndividualCustomer(
  'test-success@example.com',
  'Test',
  'Success'
);

const kycData = {
  // ... complete valid KYC data
};

await submitKycData(customer.id, kycData);

// In sandbox, status will transition: CREATED → UNDER_REVIEW → ACTIVE
// Timeline: ~30 seconds (simulated)
```

#### Scenario 2: Request for additional information

```javascript
// Submit incomplete data to trigger RFI
const customer = await createIndividualCustomer(
  'test-rfi@example.com',
  'Test',
  'RFI'
);

const incompleteKycData = {
  legalFirstName: 'Test',
  legalLastName: 'RFI',
  phoneNumber: '+14155551234',
  // Missing: address, documents, etc.
};

await submitKycData(customer.id, incompleteKycData);

// Status will become: NEEDS_ADDITIONAL_INFORMATION

// Then submit complete data
const completeKycData = {
  ...incompleteKycData,
  address: { /* complete address */ },
  documents: [ /* valid documents */ ]
};

await submitKycData(customer.id, completeKycData);

// Status should transition: UNDER_REVIEW → ACTIVE
```

#### Scenario 3: Verification rejected

```javascript
// Use specific test email to simulate rejection
const customer = await createIndividualCustomer(
  'test-rejected@example.com',
  'Test',
  'Rejected'
);

const kycData = {
  // ... complete KYC data
};

await submitKycData(customer.id, kycData);

// Status will become: REJECTED
```

### Sandbox test data

**Test email addresses** (sandbox only):
* `test-success@example.com` - Automatic approval
* `test-rfi@example.com` - Triggers RFI
* `test-rejected@example.com` - Automatic rejection
* `test-delay@example.com` - Simulates long processing (5 minutes)

{% hint style="info" %}
Sandbox verification completes in seconds or minutes instead of days. Use this to test your integration flow rapidly.
{% endhint %}

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
- [ ] Document image upload implemented
- [ ] Image quality validation (size, resolution, format)
- [ ] Secure image storage with access control
- [ ] Image URL generation for API submission
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

* Set up webhooks - [Webhook integration guide](webhooks.md)
* Understand customer states - [Customer states documentation](../concepts/customer-states.md)
* Business verification - [KYB integration guide](kyb-integration.md)
* API reference - [Customer API reference](../../../api-reference/customers.md)
* Get help - [Help & support](../../../overview/help-and-support.md)
