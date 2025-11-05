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
* For webhook integration see [Webhook integration guide](webhooks.md)
* For customer states reference see [Customer states documentation](../concepts/customer-states.md)

---

## Prerequisites

Before you begin, ensure you have:

* API credentials (API key or JWT token) - see [Get your API credentials](../../../overview/developer-dashboard.md)
* Webhook endpoint configured (recommended) - see [Webhook setup guide](webhooks.md)
* Sandbox environment access for testing
* Development environment capable of making HTTPS requests
* File upload handling for business and beneficial owner documents
* Multi-step form implementation for complex business data collection

**Estimated integration time**: 8-12 hours for complete implementation (including beneficial owner flows)

---

## Step-by-step guide

{% stepper %}
{% step %}
### Create business customer

Create a business customer profile to begin the verification process.

<!-- PHASE6_API_UPDATE: ENDPOINT - Verify customer creation endpoint -->
**Endpoint**: `POST /api/v1/customers`
<!-- /PHASE6_API_UPDATE -->

{% tabs %}
{% tab title="cURL" %}
<!-- PHASE6_API_UPDATE: BASE_URL - Verify production API base URL -->
```sh
curl --location 'https://api.example.com/api/v1/customers' \
--header 'Authorization: Bearer <API_TOKEN>' \
--header 'Content-Type: application/json' \
--data '{
  "type": "BUSINESS",
  "email": "contact@acmecorp.com",
  "businessName": "Acme Corporation"
}'
```
<!-- /PHASE6_API_UPDATE -->
{% endtab %}

{% tab title="JavaScript" %}
<!-- PHASE6_API_UPDATE: BASE_URL - Verify production API base URL -->
```javascript
async function createBusinessCustomer(email, businessName) {
  const response = await fetch('https://api.example.com/api/v1/customers', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      type: 'BUSINESS',
      email: email,
      businessName: businessName
    })
  });

  if (!response.ok) {
    const error = await response.json();
    throw new Error(`Failed to create business: ${error.message}`);
  }

  const customer = await response.json();
  console.log('Business customer created:', customer.id);
  console.log('Status:', customer.status); // "CREATED"

  return customer;
}

// Usage
const business = await createBusinessCustomer(
  'contact@acmecorp.com',
  'Acme Corporation'
);
```
<!-- /PHASE6_API_UPDATE -->
{% endtab %}

{% tab title="Python" %}
<!-- PHASE6_API_UPDATE: BASE_URL - Verify production API base URL -->
```python
import requests
import os

def create_business_customer(email, business_name):
    response = requests.post(
        'https://api.example.com/api/v1/customers',
        headers={
            'Authorization': f'Bearer {os.environ["API_KEY"]}',
            'Content-Type': 'application/json'
        },
        json={
            'type': 'BUSINESS',
            'email': email,
            'businessName': business_name
        }
    )

    response.raise_for_status()
    customer = response.json()

    print(f"Business customer created: {customer['id']}")
    print(f"Status: {customer['status']}")  # "CREATED"

    return customer

# Usage
business = create_business_customer(
    'contact@acmecorp.com',
    'Acme Corporation'
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

type CreateBusinessRequest struct {
    Type         string `json:"type"`
    Email        string `json:"email"`
    BusinessName string `json:"businessName"`
}

type Customer struct {
    ID     string `json:"id"`
    Status string `json:"status"`
    // Add other fields as needed
}

func createBusinessCustomer(email, businessName string) (*Customer, error) {
    reqBody := CreateBusinessRequest{
        Type:         "BUSINESS",
        Email:        email,
        BusinessName: businessName,
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
        return nil, fmt.Errorf("failed to create business: %s", body)
    }

    var customer Customer
    if err := json.NewDecoder(resp.Body).Decode(&customer); err != nil {
        return nil, err
    }

    fmt.Printf("Business customer created: %s\n", customer.ID)
    fmt.Printf("Status: %s\n", customer.Status)

    return &customer, nil
}

// Usage
func main() {
    business, err := createBusinessCustomer(
        "contact@acmecorp.com",
        "Acme Corporation",
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
  "id": "customer_biz123abc456",
  "type": "BUSINESS",
  "status": "CREATED",
  "email": "contact@acmecorp.com",
  "businessName": "Acme Corporation",
  "verificationLink": "https://app.example.com/verify-customer/biz123...",
  "createdAt": "2025-11-05T10:00:00Z"
}
```
<!-- /PHASE6_API_UPDATE -->

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

<!-- PHASE6_API_UPDATE: ENDPOINT - Verify customer update endpoint -->
**Endpoint**: `PATCH /api/v1/customers/:customerId`
<!-- /PHASE6_API_UPDATE -->

{% tabs %}
{% tab title="cURL" %}
<!-- PHASE6_API_UPDATE: BASE_URL - Verify production API base URL -->
```sh
curl --location --request PATCH 'https://api.example.com/api/v1/customers/customer_biz123' \
--header 'Authorization: Bearer <API_TOKEN>' \
--header 'Content-Type: application/json' \
--data '{
  "kybData": {
    "businessLegalName": "Acme Corporation",
    "businessType": "CORPORATION",
    "businessCountry": "US",
    "taxIdNumber": "12-3456789",
    "taxIdType": "EIN",
    "beneficialOwners": [
      {
        "firstName": "John",
        "lastName": "Smith",
        "ownershipPercentage": 60,
        "email": "john.smith@acmecorp.com"
      }
    ]
  }
}'
```
<!-- /PHASE6_API_UPDATE -->
{% endtab %}

{% tab title="JavaScript" %}
<!-- PHASE6_API_UPDATE: REQUEST_PAYLOAD - Validate complete KYB data structure -->
```javascript
async function submitKybData(customerId, kybData) {
  const response = await fetch(
    `https://api.example.com/api/v1/customers/${customerId}`,
    {
      method: 'PATCH',
      headers: {
        'Authorization': `Bearer ${process.env.API_KEY}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ kybData })
    }
  );

  if (!response.ok) {
    const error = await response.json();

    // Handle state-based restrictions
    if (error.error === 'PATCH_NOT_ALLOWED') {
      console.error('Cannot update: verification in progress');
      console.error('Current status:', error.customerStatus);
      throw new Error('Business data locked during verification');
    }

    throw new Error(`Failed to submit KYB data: ${error.message}`);
  }

  const customer = await response.json();
  console.log('KYB data submitted');
  console.log('New status:', customer.status); // Should transition to "UNDER_REVIEW"

  return customer;
}

// Complete KYB data example
const kybData = {
  businessLegalName: 'Acme Corporation',
  businessDba: 'ACME',
  businessType: 'CORPORATION',
  businessAddress: {
    addressLine1: '456 Business Ave',
    addressLine2: 'Suite 100',
    city: 'San Francisco',
    state: 'California',
    stateCode: 'CA',
    postalCode: '94105',
    country: 'US',
    locale: 'en-US'
  },
  businessCountry: 'US',
  businessPhoneNumber: '+14155559876',
  taxIdNumber: '12-3456789',
  taxIdType: 'EIN',
  businessWebsite: 'https://acmecorp.com',
  dateOfIncorporation: '2015-03-15',
  expectedAnnualRevenueUsd: '1M_TO_10M',
  businessPurpose: 'Software development and SaaS products',
  businessDocuments: [
    {
      type: 'CERTIFICATE_OF_INCORPORATION',
      issueDate: '2015-03-15',
      issuingAuthority: 'Delaware Secretary of State',
      frontImageUrl: 'https://your-storage.com/incorporation-cert.pdf'
    },
    {
      type: 'BUSINESS_LICENSE',
      number: 'BL-2015-12345',
      issueDate: '2015-04-01',
      expirationDate: '2026-04-01',
      issuingAuthority: 'City of San Francisco',
      frontImageUrl: 'https://your-storage.com/business-license.pdf'
    }
  ],
  beneficialOwners: [
    {
      firstName: 'John',
      lastName: 'Smith',
      email: 'john.smith@acmecorp.com',
      dateOfBirth: '1985-06-20',
      ownershipPercentage: 60,
      title: 'CEO & Founder',
      address: {
        addressLine1: '789 Owner St',
        city: 'San Francisco',
        state: 'California',
        stateCode: 'CA',
        postalCode: '94110',
        country: 'US',
        locale: 'en-US'
      },
      phoneNumber: '+14155551111',
      nationalIdType: 'SSN',
      nationalIdNumber: '123-45-6789',
      nationalIdIssuingCountry: 'US',
      nationality: 'US',
      documents: [
        {
          type: 'PASSPORT',
          number: 'P12345678',
          issuingCountry: 'US',
          expirationDate: '2030-12-31',
          frontImageUrl: 'https://your-storage.com/john-passport.jpg'
        }
      ]
    },
    {
      firstName: 'Jane',
      lastName: 'Doe',
      email: 'jane.doe@acmecorp.com',
      dateOfBirth: '1988-09-10',
      ownershipPercentage: 40,
      title: 'CTO & Co-Founder',
      address: {
        addressLine1: '321 Cofounder Ln',
        city: 'San Francisco',
        state: 'California',
        stateCode: 'CA',
        postalCode: '94107',
        country: 'US',
        locale: 'en-US'
      },
      phoneNumber: '+14155552222',
      nationalIdType: 'SSN',
      nationalIdNumber: '987-65-4321',
      nationalIdIssuingCountry: 'US',
      nationality: 'US',
      documents: [
        {
          type: 'DRIVERS_LICENSE',
          number: 'D9876543',
          issuingCountry: 'US',
          expirationDate: '2028-08-15',
          frontImageUrl: 'https://your-storage.com/jane-license-front.jpg',
          backImageUrl: 'https://your-storage.com/jane-license-back.jpg'
        }
      ]
    }
  ]
};

const updatedBusiness = await submitKybData('customer_biz123', kybData);
```
<!-- /PHASE6_API_UPDATE -->
{% endtab %}

{% tab title="Python" %}
<!-- PHASE6_API_UPDATE: REQUEST_PAYLOAD - Validate complete KYB data structure -->
```python
def submit_kyb_data(customer_id, kyb_data):
    response = requests.patch(
        f'https://api.example.com/api/v1/customers/{customer_id}',
        headers={
            'Authorization': f'Bearer {os.environ["API_KEY"]}',
            'Content-Type': 'application/json'
        },
        json={'kybData': kyb_data}
    )

    if not response.ok:
        error = response.json()

        # Handle state-based restrictions
        if error.get('error') == 'PATCH_NOT_ALLOWED':
            print('Cannot update: verification in progress')
            print(f'Current status: {error.get("customerStatus")}')
            raise ValueError('Business data locked during verification')

        raise Exception(f'Failed to submit KYB data: {error["message"]}')

    customer = response.json()
    print('KYB data submitted')
    print(f'New status: {customer["status"]}')  # Should be "UNDER_REVIEW"

    return customer

# Complete KYB data example
kyb_data = {
    'businessLegalName': 'Acme Corporation',
    'businessDba': 'ACME',
    'businessType': 'CORPORATION',
    'businessAddress': {
        'addressLine1': '456 Business Ave',
        'addressLine2': 'Suite 100',
        'city': 'San Francisco',
        'state': 'California',
        'stateCode': 'CA',
        'postalCode': '94105',
        'country': 'US',
        'locale': 'en-US'
    },
    'businessCountry': 'US',
    'businessPhoneNumber': '+14155559876',
    'taxIdNumber': '12-3456789',
    'taxIdType': 'EIN',
    'businessWebsite': 'https://acmecorp.com',
    'dateOfIncorporation': '2015-03-15',
    'expectedAnnualRevenueUsd': '1M_TO_10M',
    'businessPurpose': 'Software development and SaaS products',
    'businessDocuments': [
        {
            'type': 'CERTIFICATE_OF_INCORPORATION',
            'issueDate': '2015-03-15',
            'issuingAuthority': 'Delaware Secretary of State',
            'frontImageUrl': 'https://your-storage.com/incorporation-cert.pdf'
        }
    ],
    'beneficialOwners': [
        {
            'firstName': 'John',
            'lastName': 'Smith',
            'email': 'john.smith@acmecorp.com',
            'dateOfBirth': '1985-06-20',
            'ownershipPercentage': 60,
            'title': 'CEO & Founder',
            'address': {
                'addressLine1': '789 Owner St',
                'city': 'San Francisco',
                'state': 'California',
                'stateCode': 'CA',
                'postalCode': '94110',
                'country': 'US',
                'locale': 'en-US'
            },
            'phoneNumber': '+14155551111',
            'nationalIdType': 'SSN',
            'nationalIdNumber': '123-45-6789',
            'nationalIdIssuingCountry': 'US',
            'nationality': 'US',
            'documents': [
                {
                    'type': 'PASSPORT',
                    'number': 'P12345678',
                    'issuingCountry': 'US',
                    'expirationDate': '2030-12-31',
                    'frontImageUrl': 'https://your-storage.com/john-passport.jpg'
                }
            ]
        }
    ]
}

updated_business = submit_kyb_data('customer_biz123', kyb_data)
```
<!-- /PHASE6_API_UPDATE -->
{% endtab %}

{% tab title="Go" %}
<!-- PHASE6_API_UPDATE: REQUEST_PAYLOAD - Validate complete KYB data structure -->
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

type BusinessDocument struct {
    Type             string `json:"type"`
    Number           string `json:"number,omitempty"`
    IssueDate        string `json:"issueDate"`
    ExpirationDate   string `json:"expirationDate,omitempty"`
    IssuingAuthority string `json:"issuingAuthority"`
    FrontImageUrl    string `json:"frontImageUrl"`
}

type BeneficialOwnerDocument struct {
    Type           string `json:"type"`
    Number         string `json:"number"`
    IssuingCountry string `json:"issuingCountry"`
    ExpirationDate string `json:"expirationDate"`
    FrontImageUrl  string `json:"frontImageUrl"`
    BackImageUrl   string `json:"backImageUrl,omitempty"`
}

type BeneficialOwner struct {
    FirstName                  string                    `json:"firstName"`
    LastName                   string                    `json:"lastName"`
    Email                      string                    `json:"email"`
    DateOfBirth                string                    `json:"dateOfBirth"`
    OwnershipPercentage        float64                   `json:"ownershipPercentage"`
    Title                      string                    `json:"title"`
    Address                    Address                   `json:"address"`
    PhoneNumber                string                    `json:"phoneNumber"`
    NationalIdType             string                    `json:"nationalIdType"`
    NationalIdNumber           string                    `json:"nationalIdNumber"`
    NationalIdIssuingCountry   string                    `json:"nationalIdIssuingCountry"`
    Nationality                string                    `json:"nationality"`
    Documents                  []BeneficialOwnerDocument `json:"documents"`
}

type KybData struct {
    BusinessLegalName        string             `json:"businessLegalName"`
    BusinessDba              string             `json:"businessDba,omitempty"`
    BusinessType             string             `json:"businessType"`
    BusinessAddress          Address            `json:"businessAddress"`
    BusinessCountry          string             `json:"businessCountry"`
    BusinessPhoneNumber      string             `json:"businessPhoneNumber"`
    TaxIdNumber              string             `json:"taxIdNumber"`
    TaxIdType                string             `json:"taxIdType"`
    BusinessWebsite          string             `json:"businessWebsite,omitempty"`
    DateOfIncorporation      string             `json:"dateOfIncorporation"`
    ExpectedAnnualRevenueUsd string             `json:"expectedAnnualRevenueUsd"`
    BusinessPurpose          string             `json:"businessPurpose"`
    BusinessDocuments        []BusinessDocument `json:"businessDocuments"`
    BeneficialOwners         []BeneficialOwner  `json:"beneficialOwners"`
}

type PatchBusinessRequest struct {
    KybData KybData `json:"kybData"`
}

func submitKybData(customerID string, kybData KybData) (*Customer, error) {
    reqBody := PatchBusinessRequest{KybData: kybData}

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
        return nil, fmt.Errorf("failed to submit KYB data: %s", body)
    }

    var customer Customer
    if err := json.NewDecoder(resp.Body).Decode(&customer); err != nil {
        return nil, err
    }

    fmt.Printf("KYB data submitted. New status: %s\n", customer.Status)
    return &customer, nil
}
```
<!-- /PHASE6_API_UPDATE -->
{% endtab %}
{% endtabs %}

**Important notes:**

* Business status automatically transitions to `UNDER_REVIEW` upon successful data submission
* You cannot modify data while status is `UNDER_REVIEW` (verification in progress)
* Missing or incomplete beneficial owner data is the most common cause of requests for additional information
* Always include all beneficial owners in a single submission

{% endstep %}

{% step %}
### Monitor verification status

Monitor the business verification status using webhooks (recommended) or polling.

#### Option A: Webhooks (recommended)

<!-- PHASE6_API_UPDATE: WEBHOOK_EVENTS - Verify webhook event structure -->
```javascript
// Express.js webhook handler example
app.post('/webhooks/customer-status', async (req, res) => {
  const event = req.body;

  // Verify webhook signature (see Webhook guide)
  // ... signature verification code ...

  if (event.type === 'customer.state_changed') {
    const { customerId, oldStatus, newStatus } = event.payload;

    console.log(`Business ${customerId} status changed: ${oldStatus} → ${newStatus}`);

    switch (newStatus) {
      case 'ACTIVE':
        console.log('Business verification approved');
        await enableBusinessFeatures(customerId);
        await sendApprovalEmail(customerId);
        break;

      case 'NEEDS_ADDITIONAL_INFORMATION':
        console.log('Additional information required');
        // Common for businesses: missing beneficial owner documents
        await sendRFINotification(customerId, event.payload.requiredDocuments);
        break;

      case 'REJECTED':
        console.log('Business verification rejected');
        await sendRejectionNotification(customerId, event.payload.reason);
        break;

      case 'UNDER_REVIEW':
        console.log('Business verification in progress');
        await sendProgressUpdate(customerId);
        break;
    }
  }

  res.status(200).json({ received: true });
});
```
<!-- /PHASE6_API_UPDATE -->

For complete webhook setup instructions see the [Webhook integration guide](webhooks.md).

#### Option B: Polling

```javascript
async function pollBusinessStatus(customerId, maxAttempts = 100) {
  const pollInterval = 60000; // 60 seconds (KYB takes longer than KYC)

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
      console.log('Business verification complete:', customer.status);
      return customer;
    }

    // Continue polling
    if (['UNDER_REVIEW', 'NEEDS_ADDITIONAL_INFORMATION'].includes(customer.status)) {
      await new Promise(resolve => setTimeout(resolve, pollInterval));
      continue;
    }

    console.warn('Unexpected status:', customer.status);
    return customer;
  }

  throw new Error('Polling timeout: business verification still in progress');
}
```

{% hint style="warning" %}
Business verification typically takes 3-10 business days compared to 1-5 days for individuals. Use longer polling intervals or webhooks.
{% endhint %}

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

**Response process:**

```javascript
async function handleBusinessRFI(customerId) {
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
    console.log('Additional information required for business');

    // Notify business contact
    await notifyBusiness(customerId, {
      message: 'Additional documentation needed for business verification',
      requiredActions: [
        'Provide updated Certificate of Incorporation',
        'Upload clear government-issued ID for beneficial owner Jane Doe',
        'Clarify ownership structure (percentages must total 100%)',
        'Provide proof of business address (utility bill or lease)'
      ]
    });

    // Collect and resubmit updated data
    const updatedKybData = {
      // Add missing or corrected data
      beneficialOwners: [
        // Include complete beneficial owner data with new documents
      ],
      businessDocuments: [
        // Add new or updated business documents
      ]
    };

    // Resubmit when data is ready
    const updatedBusiness = await submitKybData(customerId, updatedKybData);
    console.log('Updated data submitted. New status:', updatedBusiness.status);
  }
}
```

{% endstep %}

{% step %}
### Verification complete

When business verification is approved, status becomes `ACTIVE` and the business can access your platform's services.

```javascript
async function activateBusiness(customerId) {
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
    console.log('Business verification complete');

    // Enable platform features
    await enableBusinessPaymentAccounts(customerId);
    await grantBusinessAPIAccess(customerId);
    await setBusinessLimits(customerId, customer.kybData.expectedAnnualRevenueUsd);

    // Notify business
    await sendEmail({
      to: customer.email,
      subject: 'Business Verification Complete - Account Activated',
      body: 'Your business verification is complete. You can now access all platform features for business accounts.'
    });

    return { success: true, customer };
  } else {
    return { success: false, status: customer.status };
  }
}
```

{% endstep %}
{% endstepper %}

---

## Required data fields

### Business information

<!-- PHASE6_API_UPDATE: REQUEST_PAYLOAD - Validate business information fields -->
| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `businessLegalName` | string | Yes | Legal registered business name | "Acme Corporation" |
| `businessDba` | string | No | "Doing Business As" name | "ACME" |
| `businessType` | string | Yes | Business entity type | "CORPORATION", "LLC", "PARTNERSHIP", "SOLE_PROPRIETORSHIP" |
| `businessCountry` | string | Yes | Country of incorporation | "US" |
| `businessPhoneNumber` | string | Yes | Business phone (E.164 format) | "+14155559876" |
| `businessWebsite` | string | No | Business website URL | "https://acmecorp.com" |
| `dateOfIncorporation` | string | Yes | Incorporation date (YYYY-MM-DD) | "2015-03-15" |
| `businessPurpose` | string | Yes | Description of business activities | "Software development and SaaS products" |
<!-- /PHASE6_API_UPDATE -->

### Business address

Same structure as individual address (see KYC guide), but represents the registered business address.

<!-- PHASE6_API_UPDATE: REQUEST_PAYLOAD - Validate business address fields -->
| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `addressLine1` | string | Yes | Street address | "456 Business Ave" |
| `addressLine2` | string | No | Suite, floor, unit | "Suite 100" |
| `city` | string | Yes | City name | "San Francisco" |
| `state` | string | Yes | State/province | "California" |
| `stateCode` | string | Yes | 2-letter state code | "CA" |
| `postalCode` | string | Yes | Zip/postal code | "94105" |
| `country` | string | Yes | ISO 3166-1 alpha-2 code | "US" |
| `locale` | string | Yes | Language and region | "en-US" |
<!-- /PHASE6_API_UPDATE -->

### Tax and registration

<!-- PHASE6_API_UPDATE: REQUEST_PAYLOAD - Validate tax and registration fields -->
| Field | Type | Required | Description | Valid Values |
|-------|------|----------|-------------|--------------|
| `taxIdNumber` | string | Yes | Tax identification number | "12-3456789" (EIN for US) |
| `taxIdType` | string | Yes | Type of tax ID | "EIN" (US), "VAT", "COMPANY_NUMBER", etc. |
<!-- /PHASE6_API_UPDATE -->

### Financial information

<!-- PHASE6_API_UPDATE: REQUEST_PAYLOAD - Validate financial information fields -->
| Field | Type | Required | Description | Valid Values |
|-------|------|----------|-------------|--------------|
| `expectedAnnualRevenueUsd` | string | Yes | Expected annual revenue | "UNDER_100K", "100K_TO_1M", "1M_TO_10M", "10M_TO_50M", "OVER_50M" |
<!-- /PHASE6_API_UPDATE -->

### Beneficial owners

You must provide complete information for all beneficial owners. See the [Beneficial owner requirements](#beneficial-owner-requirements) section for detailed requirements.

---

## Beneficial owner requirements

### Who is a beneficial owner?

A beneficial owner is any individual who:

* Owns 25% or more of the business equity, OR
* Exercises significant control over the business (C-level executives, board members)

{% hint style="info" %}
You must identify and verify all beneficial owners. Missing or incomplete beneficial owner data significantly delays verification and triggers requests for additional information.
{% endhint %}

### Beneficial owner data fields

For each beneficial owner, collect the following information:

#### Personal information

<!-- PHASE6_API_UPDATE: REQUEST_PAYLOAD - Validate beneficial owner personal information fields -->
| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `firstName` | string | Yes | Legal first name | "John" |
| `lastName` | string | Yes | Legal last name | "Smith" |
| `email` | string | Yes | Email address | "john.smith@acmecorp.com" |
| `dateOfBirth` | string | Yes | Date of birth (YYYY-MM-DD) | "1985-06-20" |
| `phoneNumber` | string | Yes | Phone (E.164 format) | "+14155551111" |
| `nationality` | string | Yes | ISO 3166-1 alpha-2 code | "US" |
<!-- /PHASE6_API_UPDATE -->

#### Ownership and role

<!-- PHASE6_API_UPDATE: REQUEST_PAYLOAD - Validate beneficial owner ownership fields -->
| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `ownershipPercentage` | number | Yes | Ownership percentage (0-100) | 60 |
| `title` | string | Yes | Role in company | "CEO & Founder", "CFO", "Board Member" |
<!-- /PHASE6_API_UPDATE -->

#### Address and identification

* **Address**: Complete residential address (same structure as business address)
* **National ID**: `nationalIdType`, `nationalIdNumber`, `nationalIdIssuingCountry`
* **Documents**: At least one government-issued photo ID

#### Beneficial owner documents

Each beneficial owner needs at least one government-issued photo ID (passport, driver's license, national ID) with the same format requirements as individual verification documents.

```javascript
// Beneficial owner document example
{
  firstName: 'John',
  lastName: 'Smith',
  ownershipPercentage: 60,
  title: 'CEO & Founder',
  // ... other fields ...
  documents: [
    {
      type: 'PASSPORT',
      number: 'P12345678',
      issuingCountry: 'US',
      expirationDate: '2030-12-31',
      frontImageUrl: 'https://your-storage.com/john-passport.jpg'
    }
  ]
}
```

### Ownership percentage validation

{% hint style="warning" %}
Ownership percentages should generally add up to 100% (or close to it). Significant discrepancies trigger requests for additional information.
{% endhint %}

```javascript
function validateOwnershipPercentages(beneficialOwners) {
  const total = beneficialOwners.reduce((sum, owner) => sum + owner.ownershipPercentage, 0);

  if (total < 95 || total > 105) {
    console.warn(`Warning: Ownership percentages total ${total}%. Expected ~100%.`);
    console.warn('This may trigger a request for additional information.');
  }

  return total >= 95 && total <= 105;
}

// Usage
const isValid = validateOwnershipPercentages(kybData.beneficialOwners);
if (!isValid) {
  // Prompt user to review ownership structure
}
```

---

## Document requirements

### Business documents

**Required documents:**

* Certificate of Incorporation or equivalent formation document
* Business License or registration certificate (if applicable)
* Proof of Business Address (utility bill, lease agreement, bank statement)

**Optional documents** (may speed up verification):

* Articles of Organization
* Operating Agreement or Bylaws
* Tax Registration Certificate
* Professional Licenses (for regulated industries)
* Bank Account Verification Letter

### Business document format

<!-- PHASE6_API_UPDATE: REQUEST_PAYLOAD - Validate business document structure -->
```javascript
{
  type: 'CERTIFICATE_OF_INCORPORATION',
  number: 'CORP-2015-12345',          // Optional: document number
  issueDate: '2015-03-15',            // ISO 8601 date
  expirationDate: '2025-03-15',       // Optional: if applicable
  issuingAuthority: 'Delaware Secretary of State',
  frontImageUrl: 'https://...',       // URL to document image/PDF
  backImageUrl: 'https://...'         // Optional: if multi-page
}
```
<!-- /PHASE6_API_UPDATE -->

### Valid business document types

| Document Type | Description | When Required |
|--------------|-------------|---------------|
| `CERTIFICATE_OF_INCORPORATION` | Formation document | Always |
| `BUSINESS_LICENSE` | Business operating license | If applicable to jurisdiction |
| `ARTICLES_OF_ORGANIZATION` | LLC formation document | Alternative to incorporation certificate |
| `OPERATING_AGREEMENT` | Business operating rules | Recommended for LLCs |
| `TAX_REGISTRATION` | Tax registration certificate | Some jurisdictions |
| `PROOF_OF_ADDRESS` | Utility bill, lease | Recommended |
| `BANK_VERIFICATION` | Bank letter confirming business | Recommended |

### Document image requirements

Same requirements as individual verification documents (see KYC Integration Guide), with additional considerations:

* **Multi-page documents**: Upload as single PDF or multiple images
* **Scanned documents**: Ensure scans are clear and complete
* **Certified copies**: Original or certified copies preferred
* **Language**: Documents in non-English languages may require certified translation

---

## Complex scenarios

### Multiple beneficial owners

When there are 3+ beneficial owners:

```javascript
const kybData = {
  // ... business information ...
  beneficialOwners: [
    {
      firstName: 'Owner',
      lastName: 'One',
      ownershipPercentage: 40,
      // ... complete owner data ...
    },
    {
      firstName: 'Owner',
      lastName: 'Two',
      ownershipPercentage: 35,
      // ... complete owner data ...
    },
    {
      firstName: 'Owner',
      lastName: 'Three',
      ownershipPercentage: 25,
      // ... complete owner data ...
    }
  ]
};

// Validate total ownership
const totalOwnership = kybData.beneficialOwners.reduce(
  (sum, owner) => sum + owner.ownershipPercentage,
  0
);
console.log('Total ownership:', totalOwnership); // Should be ~100
```

### Corporate ownership structure

When a business is owned by another business entity:

{% hint style="info" %}
If a business is owned by another corporation, you must identify the ultimate beneficial owners (individuals) who own 25%+ of the parent company.
{% endhint %}

**Example**: Corporation A owns 60% of your business. John Smith owns 50% of Corporation A.

* **Report**: John Smith as beneficial owner with 30% ownership (60% × 50%)
* **Include**: Corporation A details in business purpose or structure notes

### Trust or foundation ownership

When ownership is held through trusts, report:

* Trustees with control over trust assets
* Beneficiaries who will eventually receive trust assets
* Settlors/grantors who established the trust

Include trust documentation as additional business documents.

### No single owner above 25%

When no individual owns 25%+ but multiple individuals have significant control, report all C-level executives and board members with significant control, even if ownership is below 25%.

```javascript
const kybData = {
  // ... business information ...
  beneficialOwners: [
    {
      firstName: 'CEO',
      lastName: 'Executive',
      ownershipPercentage: 15, // Below 25%, but has control
      title: 'Chief Executive Officer',
      // ... complete data ...
    },
    {
      firstName: 'CFO',
      lastName: 'Executive',
      ownershipPercentage: 12, // Below 25%, but has control
      title: 'Chief Financial Officer',
      // ... complete data ...
    }
  ]
};
```

---

## Error handling

### Common business verification errors

#### Missing beneficial owner data

<!-- PHASE6_API_UPDATE: ERROR_CODES - Verify error response structure -->
**Error:**

```json
{
  "error": "BAD_REQUEST",
  "message": "Validation failed: At least one beneficial owner must be provided",
  "statusCode": 400
}
```
<!-- /PHASE6_API_UPDATE -->

**Solution**: Ensure `beneficialOwners` array has at least one complete entry.

#### Invalid ownership percentages

<!-- PHASE6_API_UPDATE: ERROR_CODES - Verify error response structure -->
**Error:**

```json
{
  "error": "BAD_REQUEST",
  "message": "Validation failed: Ownership percentages should total approximately 100%",
  "statusCode": 400
}
```
<!-- /PHASE6_API_UPDATE -->

**Solution**: Review ownership structure. Percentages should be realistic and add up to approximately 100%.

```javascript
// Validate before submission
function validateBusinessData(kybData) {
  const errors = [];

  // Check beneficial owners exist
  if (!kybData.beneficialOwners || kybData.beneficialOwners.length === 0) {
    errors.push('At least one beneficial owner must be provided');
  }

  // Validate ownership percentages
  if (kybData.beneficialOwners) {
    const total = kybData.beneficialOwners.reduce(
      (sum, owner) => sum + (owner.ownershipPercentage || 0),
      0
    );

    if (total < 95 || total > 105) {
      errors.push(`Ownership percentages total ${total}%, expected ~100%`);
    }

    // Check each owner has required data
    kybData.beneficialOwners.forEach((owner, index) => {
      if (!owner.firstName || !owner.lastName) {
        errors.push(`Beneficial owner ${index + 1}: Missing name`);
      }
      if (!owner.documents || owner.documents.length === 0) {
        errors.push(`Beneficial owner ${index + 1}: Missing ID documents`);
      }
      if (!owner.ownershipPercentage) {
        errors.push(`Beneficial owner ${index + 1}: Missing ownership percentage`);
      }
    });
  }

  // Validate business type
  const validTypes = ['CORPORATION', 'LLC', 'PARTNERSHIP', 'SOLE_PROPRIETORSHIP', 'NONPROFIT'];
  if (!validTypes.includes(kybData.businessType)) {
    errors.push(`Invalid business type: ${kybData.businessType}`);
  }

  // Validate EIN format (US businesses)
  if (kybData.taxIdType === 'EIN' && !/^\d{2}-\d{7}$/.test(kybData.taxIdNumber)) {
    errors.push('EIN must be in format: 12-3456789');
  }

  return errors;
}

// Usage
const errors = validateBusinessData(kybData);
if (errors.length > 0) {
  console.error('Validation errors:', errors);
  // Display errors to user
  return;
}

await submitKybData(customerId, kybData);
```

#### PATCH not allowed

See [Individual customer verification (KYC)](kyc-integration.md) for PATCH restriction handling.

---

## Testing in sandbox

<!-- PHASE6_API_UPDATE: BASE_URL - Verify sandbox API base URL -->
### Sandbox environment

**Base URL**: `https://api.sandbox.example.com/api/v1`
<!-- /PHASE6_API_UPDATE -->

### Test scenarios

#### Successful business verification

```javascript
const business = await createBusinessCustomer(
  'test-business-success@example.com',
  'Test Success Corp'
);

const kybData = {
  businessLegalName: 'Test Success Corp',
  businessType: 'CORPORATION',
  businessCountry: 'US',
  // ... complete valid KYB data with at least 1 beneficial owner ...
  beneficialOwners: [
    {
      firstName: 'Test',
      lastName: 'Owner',
      ownershipPercentage: 100,
      // ... complete owner data ...
    }
  ]
};

await submitKybData(business.id, kybData);

// Status will transition: CREATED → UNDER_REVIEW → ACTIVE
// Timeline: ~60 seconds (simulated)
```

#### Missing beneficial owners (triggers request for additional information)

```javascript
const business = await createBusinessCustomer(
  'test-business-rfi@example.com',
  'Test RFI Corp'
);

const incompleteKybData = {
  businessLegalName: 'Test RFI Corp',
  businessType: 'CORPORATION',
  // Missing: beneficial owners
};

await submitKybData(business.id, incompleteKybData);

// Status will become: NEEDS_ADDITIONAL_INFORMATION

// Add beneficial owners and resubmit
const completeKybData = {
  ...incompleteKybData,
  beneficialOwners: [ /* complete owner data */ ]
};

await submitKybData(business.id, completeKybData);
// Status should transition: UNDER_REVIEW → ACTIVE
```

#### Complex ownership structure

```javascript
const business = await createBusinessCustomer(
  'test-business-complex@example.com',
  'Test Complex Corp'
);

const kybData = {
  businessLegalName: 'Test Complex Corp',
  businessType: 'CORPORATION',
  // ... business data ...
  beneficialOwners: [
    { ownershipPercentage: 40, /* ... */ },
    { ownershipPercentage: 35, /* ... */ },
    { ownershipPercentage: 25, /* ... */ }
  ]
};

await submitKybData(business.id, kybData);
// Simulates verification of complex structure
```

### Sandbox test data

**Test email addresses** (sandbox only):

* `test-business-success@example.com` - Automatic approval
* `test-business-rfi@example.com` - Triggers request for additional information (missing beneficial owners)
* `test-business-rejected@example.com` - Automatic rejection
* `test-business-delay@example.com` - Simulates long processing (10 minutes)

---

## Production checklist

### API integration

* [ ] Authentication working correctly
* [ ] Business customer creation integrated
* [ ] KYB data submission (PATCH) integrated
* [ ] Status monitoring implemented (webhooks or polling)
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

* For webhook setup see [Webhook integration guide](webhooks.md)
* For customer state definitions see [Customer states documentation](../concepts/customer-states.md)
* For individual verification see [Individual customer verification (KYC)](kyc-integration.md)
* For API reference see [Customer API reference](../../../api-reference/customers.md)
* For troubleshooting see [Help & support](../../../overview/help-and-support.md)
