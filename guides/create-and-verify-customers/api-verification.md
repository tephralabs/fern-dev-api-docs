# Verify customers via API

## Overview

Fern supports customer verification via API, allowing you to onboard users without redirecting them to Fern's hosted KYC forms. This guide explains how to use the API-based verification flow, when to use it, and how it differs from the hosted form approach.

- For the traditional hosted form flow, see [Create and verify customers](./README.md).

## Step-by-step guide

{% stepper %}
{% step %}
### Create a customer with KYC data

Create a customer via the [Customers API](../../api-reference/customers.md), including the `kycData` object in your request body.

**Sample individual customer with KYC data POST request:**

{% tabs %}
{% tab title="JSON" %}
```json
{
  "customerType": "INDIVIDUAL",
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@example.com",
  "kycData": {
    "legalFirstName": "John",
    "legalLastName": "Doe",
    "phoneNumber": "+12223334444",
    "dateOfBirth": "1990-01-15",
    "address": {
      "streetLine1": "123 Main St",
      "city": "New York",
      "stateRegionProvince": "NY",
      "postalCode": "10001",
      "countryCode": "US"
    },
    "taxIdNumber": "123-45-6789",
    "documents": [
      {
        "type": "GOVERNMENT_ID",
        "subtype": "PASSPORT",
        "countryCode": "US",
        "documentIdNumber": "123456789",
        "issuanceDate": "2020-01-15",
        "expirationDate": "2030-01-15",
        "frontIdImage": "data:image/jpeg;base64,..."
      },
      {
        "type": "PROOF_OF_ADDRESS",
        "subtype": "UTILITY_BILL",
        "description": "Electric bill from January 2024",
        "proofOfAddressImage": "data:application/pdf;base64,..."
      }
    ],
    "employmentStatus": "EMPLOYED",
    "mostRecentOccupation": "Software Engineer",
    "sourceOfFunds": "SALARY",
    "accountPurpose": "PERSONAL_EXPENSES",
    "expectedMonthlyPaymentsUsd": "LESS_THAN_5000",
    "isIntermediary": false
  }
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**How to generate base64-encoded fields:**

You can generate a base64-encoded string from a file using the following command:

```sh
echo -n 'data:<mime-type>;base64,'$(base64 -i path/to/your-file | tr -d '\n')
```

- Replace `<mime-type>` with the correct MIME type for your file (e.g., `image/jpeg`, `image/png`, `application/pdf`).
- Replace `path/to/your-file` with your actual file path.
- The output can be used for any field that requires a base64-encoded file.
- **Recommended file size:** The encoded file should be **greater than 10KB and less than 3MB**.

**⚠️ Make sure that the file type and the MIME type in the prefix match.** For example, if your file is a JPEG image, use `data:image/jpeg;base64,` and a `.jpg` or `.jpeg` file.
{% endhint %}
{% endstep %}

{% step %}
### Receive immediate feedback

If the request is valid, you'll receive a response with the customer ID, status, and a hosted KYC link (for fallback/manual completion).

{% tabs %}
{% tab title="JSON" %}
```json
{
  "customerId": "765971b5-8a4e-4a54-a294-710a4636c987",
  "customerType": "INDIVIDUAL",
  "customerStatus": "UNDER_REVIEW",
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@example.com",
  "kycLink": "https://app.fernhq.com/verify-customer/765971b5-8a4e-4a54-a294-710a4636c987",
  "updatedAt": "2025-04-30T15:40:40.832Z"
}
```
{% endtab %}
{% endtabs %}

If there are validation errors, you'll receive a detailed error response:

{% tabs %}
{% tab title="JSON" %}
```json
{
  "code": "BAD_REQUEST",
  "message": "body/kycData/expectedMonthlyPaymentsUsd must be equal to constant...",
  "details": {
    "issues": [
      {
        "path": ["kycData", "expectedMonthlyPaymentsUsd"],
        "message": "must be equal to constant",
        "keyword": "const",
        "params": {
          "allowedValue": "LESS_THAN_5000"
        }
      }
    ],
    "method": "POST",
    "url": "/customers"
  }
}
```
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
### Monitor verification status

Check the customer status using the [Customers API](../../api-reference/customers.md) `GET` endpoint.

See [Additional Details](./additional-details.md) for status definitions and other KYC-related information.
{% endstep %}

{% step %}
### Update KYC data

If you need to add KYC data to an existing customer or if additional information is requested, you can update the customer using the PATCH endpoint.
{% hint style="warning" %}
**Important restrictions for KYC updates:**

- You cannot update KYC data while verification is in progress
- You can submit partial KYC data updates, only including the fields you want to add or update
{% endhint %}
**Update customer with KYC data PATCH request:**

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X PATCH https://api.fernhq.com/customers/{customer_id} \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{
    "kycData": {
      "legalFirstName": "John",
      "legalLastName": "Doe",
      "phoneNumber": "+12223334444",
      "dateOfBirth": "1990-01-15",
      "address": {
        "streetLine1": "123 Main St",
        "city": "New York",
        "stateRegionProvince": "NY",
        "postalCode": "10001",
        "countryCode": "US"
      },
      "taxIdNumber": "123-45-6789",
      "documents": [
        {
          "type": "GOVERNMENT_ID",
          "subtype": "DRIVERS_LICENSE",
          "countryCode": "US",
          "documentIdNumber": "D123456789",
          "issuanceDate": "2020-01-15",
          "expirationDate": "2030-01-15",
          "frontIdImage": "data:image/jpeg;base64,...",
          "backIdImage": "data:image/jpeg;base64,..."
        }
      ],
      "employmentStatus": "EMPLOYED",
      "mostRecentOccupation": "Software Engineer",
      "sourceOfFunds": "SALARY",
      "accountPurpose": "PERSONAL_EXPENSES",
      "expectedMonthlyPaymentsUsd": "LESS_THAN_5000",
      "isIntermediary": false
    }
  }'
```
{% endtab %}
{% endtabs %}
**Response:**
The response will be the same as the create customer response, with the customer status updated to `UNDER_REVIEW` if the KYC data was successfully added.
{% endstep %}

{% step %}
### Handle additional information requests

If more information is needed, the status will change to `NEEDS_ADDITIONAL_INFORMATION`.

{% hint style="info" %}
Currently, handling additional information requests requires manual intervention. You cannot update KYC data via API once it has been submitted. Contact support to provide additional information.
{% endhint %}
{% endstep %}

{% step %}
### Completion

Once approved, the customer status will be `ACTIVE`. If rejected, it will be `REJECTED`.
{% endstep %}
{% endstepper %}

## Key Points

- All PII is encrypted
- KYC data can only be submitted once per customer via API
- You can use PATCH to add `kycData` to a customer that doesn't have it yet
- Once submitted, KYC data cannot be modified via API
- Hosted KYC link is always returned for fallback/manual completion
- Status updates are available via API
- The verification process starts automatically when KYC data is submitted
- See [Additional Details](./additional-details.md) for status definitions and other KYC-related information
