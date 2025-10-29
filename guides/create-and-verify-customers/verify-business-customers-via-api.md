# Verify Business customers via API

## Overview

Fern supports business verification via API, allowing you to onboard business customers without redirecting them to Fern's hosted KYB forms. This guide explains how to use the API-based verification flow for business customers, when to use it, and how it differs from the hosted form approach.

**Related verification guides:**

* For using Fern's hosted KYB forms see [Verify Business customers via hosted form](verify-business-customers-via-hosted-form.md)
* For API-based individual (KYC) verification see [Verify Individual customers via API](api-verification.md)
* For list of KYB requirements see [Requirements for businesses](requirements-for-businesses.md)

## Step-by-step guide

{% stepper %}
{% step %}
#### Create a business customer with KYB data

Create a business customer via the [Customers API](../../api-reference/customers.md), including the `kybData` object in your request body.

**Sample business customer with KYB data POST request:**

{% tabs %}
{% tab title="JSON" %}
```json
{
  "customerType": "BUSINESS",
  "businessName": "Acme Corporation",
  "email": "contact@acme.com",
  "kybData": {
    "businessInfo": {
      "legalBusinessName": "Acme Corporation Inc.",
      "dbaName": "Acme Corp",
      "businessType": "LLC",
      "businessIndustries": "541511",
      "businessDescription": "Software development and technology consulting services for enterprise clients",
      "website": "https://www.acme.com",
      "otherLinks": [
        "https://linkedin.com/company/acmecorp"
      ],
      "formationDate": "2020-01-15",
      "registrationCountry": "US",
      "isDao": false
    },
    "businessRegisteredAddress": {
      "streetLine1": "123 Business St",
      "streetLine2": "Suite 100",
      "city": "San Francisco",
      "stateRegionProvince": "CA",
      "postalCode": "94105",
      "countryCode": "US"
    },
    "businessOperatingAddress": {
      "streetLine1": "456 Operating St",
      "streetLine2": "Floor 2",
      "city": "San Francisco",
      "stateRegionProvince": "CA",
      "postalCode": "94105",
      "countryCode": "US"
    },
    "businessRegistrationNumber": "12-3456789",
    "sourceOfFunds": "SALE_OF_GOODS_AND_SERVICES",
    "sourceOfFundsDescription": "Revenue from software licensing and consulting services",
    "accountPurpose": "COMPANY_OPERATIONS",
    "expectedMonthlyVolumeUsd": "BETWEEN_100000_999999",
    "estimatedAnnualRevenueUsd": "BETWEEN_1000000_9999999",
    "conductsMoneyServices": false,
    "operatesInProhibitedCountries": false,
    "highRiskActivities": ["NONE_OF_THE_ABOVE"],
    "associatedPersons": [
      {
        "legalFirstName": "Jane",
        "legalMiddleName": "Marie",
        "legalLastName": "Smith",
        "email": "jane.smith@acmecorp.com",
        "phoneNumber": "+14155551234",
        "dateOfBirth": "1985-06-20",
        "address": {
          "streetLine1": "789 Executive Ave",
          "streetLine2": "Apt 12",
          "city": "San Francisco",
          "stateRegionProvince": "CA",
          "postalCode": "94105",
          "countryCode": "US"
        },
        "ownershipPercentage": 0.45,
        "title": "Chief Executive Officer",
        "isControlPerson": true,
        "isSigner": true,
        "isDirector": true,
        "nationalIdIssuingCountry": "US",
        "nationalIdType": "SSN",
        "nationalIdNumber": "987-65-4321",
        "nationality": "US",
        "documents": [
          {
            "type": "GOVERNMENT_ID",
            "subtype": "DRIVERS_LICENSE",
            "countryCode": "US",
            "documentIdNumber": "D987654321",
            "issuanceDate": "2021-03-10",
            "expirationDate": "2029-03-10",
            "frontIdImage": "data:image/jpeg;base64,...",
            "backIdImage": "data:image/jpeg;base64,..."
          },
          {
            "type": "PROOF_OF_ADDRESS",
            "subtype": "BANK_STATEMENT",
            "description": "Bank statement for Jane Smith",
            "proofOfAddressImage": "data:application/pdf;base64,..."
          }
        ]
      }
    ],
    "businessDocuments": [
      {
        "type": "ARTICLES_OF_INCORPORATION",
        "description": "Articles of Incorporation filed in Delaware",
        "documentImage": "data:application/pdf;base64,..."
      },
      {
        "type": "OPERATING_AGREEMENT",
        "description": "LLC Operating Agreement with ownership details",
        "documentImage": "data:application/pdf;base64,..."
      },
      {
        "type": "BANK_STATEMENT",
        "description": "Business bank statement from January 2025",
        "documentImage": "data:application/pdf;base64,..."
      }
    ]
  }
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**Important field notes:**

* `businessIndustries`: Must be a valid 6-digit NAICS (North American Industry Classification System) code. Example: "541511" for Custom Computer Programming Services
* `formationDate`: Must be a valid date (ISO 8601 format: YYYY-MM-DD). Example: "2025-05-24"

At least one associated person is required. They can be:

* **Beneficial owners**: Own 25% or more (`ownershipPercentage` ≥ 0.25)
* **Control persons**: Have significant responsibility (`isControlPerson: true`)
* **Directors**: Serve on the board (`isDirector: true`)
* **Signers**: Have signing authority (`isSigner: true`)

**How to generate base64-encoded fields:**

You can generate a base64-encoded string from a file using the following command:

```sh
echo -n 'data:<mime-type>;base64,'$(base64 -i path/to/your-file | tr -d '\n')
```

* Replace `<mime-type>` with the correct MIME type for your file (e.g., `image/jpeg`, `image/png`, `application/pdf`).
* Replace `path/to/your-file` with your actual file path.
* The output can be used for any field that requires a base64-encoded file.
* **Recommended file size:** The encoded file should be **greater than 10KB and less than 10MB**.

**⚠️ Make sure that the file type and the MIME type in the prefix match.** For example, if your file is a JPEG image, use `data:image/jpeg;base64,` and a `.jpg` or `.jpeg` file.
{% endhint %}
{% endstep %}

{% step %}
#### **Receive immediate feedback**

If the request is valid, you'll receive a response with the customer ID, status, and a hosted verification link (for fallback/manual completion).

{% tabs %}
{% tab title="JSON" %}
```json
{
  "customerId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "customerType": "BUSINESS",
  "customerStatus": "UNDER_REVIEW",
  "name": "Acme Corporation",
  "email": "contact@acmecorp.com",
  "verificationLink": "https://forms.fernhq.com/verify-customer/f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "updatedAt": "2025-04-30T15:40:40.832Z",
  "organizationId": "8469411c-48c1-4e26-a032-44688be9cb4b"
}
```
{% endtab %}
{% endtabs %}

If there are validation errors, you'll receive a detailed error response:

{% tabs %}
{% tab title="JSON" %}
```json
{
  "code": "VALIDATION_ERROR",
  "message": "body/kybData/associatedPersons must have at least 1 item",
  "details": {
    "issues": [
      {
        "path": ["kybData", "associatedPersons"],
        "message": "must have at least 1 item"
      }
    ]
  }
}
```
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
#### Monitor verification status

Check the customer status using the [Customers API](../../api-reference/customers.md) `GET` endpoint.

**Query parameters:**

* `includeVerification`: Set to `true` to include full verification details in the response

See [Additional Details](additional-details.md) for status definitions and more information.


{% endstep %}

{% step %}
#### **Update** KYB **data**

You can update the customer using the PATCH endpoint.

{% hint style="warning" %}
**Important restrictions:**

* Cannot update KYB data during `UNDER_REVIEW` status
* Can submit partial updates to add/modify fields
* The PATCH endpoint follows the same schema as the POST endpoint for `kybData`
* Core customer information (`businessName`, `email`) cannot be updated
{% endhint %}

**Update customer with KYB data PATCH request:**

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X PATCH https://api.fernhq.com/customers/{customer_id} \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{
    "kybData": {
      "businessDocuments": [
        {
          "type": "BANK_STATEMENT",
          "description": "Updated bank statement",
          "documentImage": "data:application/pdf;base64,..."
        }
      ]
    }
  }'
```
{% endtab %}
{% endtabs %}

**Response:** The response will be the same as the create customer response, with the customer status updated to `UNDER_REVIEW` if the KYB data was successfully added.
{% endstep %}

{% step %}
#### **Handle additional information requests**

Status will change to `NEEDS_ADDITIONAL_INFORMATION` if more info is needed.

{% hint style="info" %}
Currently, handling additional information requests requires manual intervention. You cannot update KYC data via API once verification is in progress (`UNDER_REVIEW` status). Contact support to provide additional information.
{% endhint %}
{% endstep %}

{% step %}
#### **Completion**

Once approved, the customer status will be `ACTIVE`. If rejected, it will be `REJECTED`.
{% endstep %}
{% endstepper %}

### Understanding Associated Persons

**Beneficial Owners**

* Own 25% or more of the business
* `ownershipPercentage` must be 0.25-1.0
* Must provide complete KYC information

**Control Persons**

* Significant responsibility (CEO, CFO, etc.)
* `isControlPerson: true`
* Must provide complete KYC information

**Multiple Associated Persons**

{% tabs %}
{% tab title="json" %}
```bash
"associatedPersons": [
  {
    "legalFirstName": "Jane",
    "legalLastName": "Smith",
    "ownershipPercentage": 0.45,
    "title": "CEO",
    "isControlPerson": true,
    "isSigner": true,
    // ... other fields
  },
  {
    "legalFirstName": "John",
    "legalLastName": "Doe",
    "ownershipPercentage": 0.35,
    "title": "CTO",
    "isControlPerson": false,
    // ... other fields
  },
  {
    "legalFirstName": "Alice",
    "legalLastName": "Johnson",
    "ownershipPercentage": 0.20,
    "title": "CFO",
    "isControlPerson": true,
    // ... other fields
  }
]
```
{% endtab %}
{% endtabs %}

## Key Points

* All PII is encrypted
* Minimum 1 associated person required
* Total ownership must account for 100%
* Business documents must include formation, ownership, and proof of address
* Cannot modify KYB data during `UNDER_REVIEW`
* `verificationLink` always returned for fallback
* See [Additional Details](additional-details.md) for status definitions and other related information
