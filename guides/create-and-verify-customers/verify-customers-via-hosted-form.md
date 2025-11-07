# Verify customers via hosted form

## Overview

Fern has hosted forms that simplify the KYC data collection for your Customers. We often see our partners use Fern hosted forms during initial implementation to get out an MVP of the experience, and then transitioning to building their own KYC forms over time. This guide shows you how to use the Fern hosted forms to verify your customers.

**Related verification guides:**

* For API-based individual (KYC) verification see [Verify Individual customers via API](api-verification.md)
* For detailed KYC integration guide see [Customer Verification (KYC/KYB)](../kyc-kyb-verification/integration-guides/kyc-integration.md)
* For using Fern's hosted KYB forms see [Verify Business customers via hosted form](verify-business-customers-via-hosted-form.md)
* For list of KYC requirements see [Requirements for individuals](requirements-for-individuals.md)

## Step-by-step guide

{% stepper %}
{% step %}
#### Create an individual customer

Create an individual customer via the [Customers API](../../api-reference/customers.md). If the customer creation call succeeds, the response will include a hosted verification link that you can share with your customer directly. Customer status will be `CREATED`.

**Sample individual customer POST request :**

{% tabs %}
{% tab title="JSON" %}
```json
{
    "customerType": "INDIVIDUAL",
    "email": "bob@builder.com",
    "firstName": "Bob",
    "lastName": "The Builder"
}
```
{% endtab %}
{% endtabs %}

**Sample response:**

{% tabs %}
{% tab title="JSON" %}
```json
{
  "customerId": "5be4866c-4d85-4861-903d-a6295dfdf1e1",
  "customerStatus": "CREATED",
  "email": "bob@builder.com",
  "customerType": "INDIVIDUAL",
  "name": "Bob The Builder",
  "verificationLink": "https://forms.fernhq.com/verify-customer/5be4866c-4d85-4861-903d-a6295dfdf1e1",
  "updatedAt": "2025-04-29T13:31:25.954Z",
  "organizationId": "73fc5722-ae38-4530-a45f-db2df8f69810"
}
```
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
#### Customer completes verification via verification link

You can share the hosted KYC form link (`verificationLink`) directly with your end user for completion, or your team can complete the form on your customer's behalf.

The hosted KYC form will collect:

* Personal information (legal name, phone, date of birth, address)
* National ID information (SSN/TIN or equivalent)
* Government-issued photo ID (upload)
* Employment and occupation details
* Source of funds and account purpose
* Expected transaction volume
* Proof of address (required for SEPA access)

Once the customer completes the form, their status will change from `CREATED` to `UNDER_REVIEW`.

**What customers need to prepare:**

* Government-issued photo ID (passport, driver's license, or national ID)
* Proof of address document (bank statement, utility bill, or government correspondence dated within 90 days)
* Employment and financial information
* National identification number (SSN, TIN, or equivalent)

**Typical completion time:** Less than 5 minutes
{% endstep %}

{% step %}
#### Get customer status

You can use the [Customers API](../../api-reference/customers.md) `GET` endpoint to check for updates to the customer status. Status updates will also be communicated via webhooks.

**Sample response:**

{% tabs %}
{% tab title="JSON" %}
```json
{
  "customerId": "5be4866c-4d85-4861-903d-a6295dfdf1e1",
  "customerStatus": "ACTIVE",
  "email": "bob@builder.com",
  "customerType": "INDIVIDUAL",
  "name": "Bob The Builder",
  "verificationLink": "https://forms.fernhq.com/verify-customer/5be4866c-4d85-4861-903d-a6295dfdf1e1",
  "updatedAt": "2025-04-29T16:43:09.261Z",
  "organizationId": "73fc5722-ae38-4530-a45f-db2df8f69810"
}
```
{% endtab %}
{% endtabs %}

If any additional information is needed to verify your customer, the Fern compliance team will contact them directly to minimize compliance overhead for your team. Once the customer is fully approved, their status will change to `ACTIVE`.

For a full list of customer statuses, refer to [Customer Statuses](additional-details.md#customer-statuses).
{% endstep %}
{% endstepper %}
