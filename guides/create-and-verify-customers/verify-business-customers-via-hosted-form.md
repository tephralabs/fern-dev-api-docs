# Verify Business customers via hosted form

## Overview

Fern has hosted forms that simplify the KYC/KYB data collection for your Customers. We often see our partners use Fern hosted forms during initial implementation to get out an MVP of the experience, and then transitioning to building their own verification forms over time. This guide shows you how to use the Fern hosted forms to verify your customers.

**Related verification guides:**

* For using Fern's hosted KYB forms see [Verify Business customers via hosted form](verify-business-customers-via-hosted-form.md)
* For detailed KYB integration guide see [Customer Verification (KYC/KYB)](../kyc-kyb-verification/integration-guides/kyb-integration.md)
* For API-based individual (KYC) verification see [Verify Individual customers via API](api-verification.md)
* For list of KYB requirements see [Requirements for businesses](requirements-for-businesses.md)

## Step-by-step guide

{% stepper %}
{% step %}
#### Create a business customer

Create a business customer via the [Customers API](../../api-reference/customers.md). If the customer creation call succeeds, the response will include a hosted verification link that you can share with your customer directly. Customer status will be `CREATED`.

**Sample business customer POST request :**

{% tabs %}
{% tab title="JSON" %}
```json
{
  "customerType": "BUSINESS",
  "email": "contact@builder.com",
  "businessName": "Bob's Building Business"
}
```
{% endtab %}
{% endtabs %}

**Sample response:**

{% tabs %}
{% tab title="JSON" %}
```json
{
  "customerId": "8250d155-8785-4697-b6db-1d3800d0da5f",
  "customerStatus": "CREATED",
  "email": "contact@builder.com",
  "customerType": "BUSINESS",
  "name": "Bobs Building Business",
  "verificationLink": "https://forms.fernhq.com/verify-customer/8250d155-8785-4697-b6db-1d3800d0da5f",
  "updatedAt": "2025-10-27T16:44:49.873Z",
  "organizationId": "73fc5722-ae38-4530-a45f-db2df8f69810"
}
```
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
#### Primary contact completes the initial KYB form

You can share the hosted KYB form link (`verificationLink`) directly with your business contact for completion, or your team can complete the form on your customer's behalf.

The hosted KYB form will collect:

* Legal business name and DBA
* Business type and industry (NAICS code)
* Business description and website
* Formation date and registration number
* Registered and operating addresses
* Source of funds and account purpose
* Expected transaction volume and annual revenue
* Risk assessment information

**Beneficial Owners and Controllers:**

* Information for all individuals who own 25% or more of the business
* Information for at least one control person (CEO, CFO, Managing Member, etc.)
* Each person's complete KYC information

**Business Documents:**

* Formation documents
* Ownership documents
* Business proof of address (bank statement dated within 90 days)

Once the customer completes the form, their status will change from `CREATED` to `UNDER_REVIEW`.

**What business customers need to prepare:**

* Business formation documents
* Business registration number (EIN for US businesses)
* Ownership structure documentation
* Business bank statement or proof of address
* Government-issued photo IDs for all beneficial owners and controllers
* Proof of address for all beneficial owners and controllers
* Business description and financial information

**Typical completion time:** 10-15 minutes
{% endstep %}

{% step %}
#### Associated persons complete verification

Once the primary contact submits the initial business form, **Fern will automatically send separate verification links** to the email addresses provided for each beneficial owner and controller.

Each individual must open their unique link and complete their own corresponding KYC form.

**What each beneficial owner/controller needs to prepare:**

* Government-issued photo ID (passport, driver's license, or national ID)
* Proof of address document (bank statement, utility bill, etc., dated within 90 days)
* Personal information (phone, date of birth, address)
* National identification number (SSN, TIN, or equivalent)

The business customer's status will change from `CREATED` to `UNDER_REVIEW` only after _all_ associated persons (the primary contact, all beneficial owners, and the control person) have successfully submitted their required forms.

**Typical completion time for all forms:** 10-15 minutes (per person).
{% endstep %}

{% step %}
#### Get customer status

You can use the [Customers API](../../api-reference/customers.md) `GET` endpoint to check for updates to the customer status. Status updates will also be communicated via webhooks.

**Sample response:**

{% tabs %}
{% tab title="JSON" %}
```json
{
  "customerId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "customerStatus": "ACTIVE",
  "email": "contact@acmecorp.com",
  "customerType": "BUSINESS",
  "name": "Acme Corporation",
  "businessName": "Acme Corporation",
  "verificationLink": "https://forms.fernhq.com/verify-customer/f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "updatedAt": "2025-04-29T16:43:09.261Z",
  "organizationId": "73fc5722-ae38-4530-a45f-db2df8f69810"
}
```
{% endtab %}
{% endtabs %}

If any additional information is needed to verify your business customer, the Fern compliance team will contact them directly to minimize compliance overhead for your team. Once the business is fully approved, their status will change to `ACTIVE`.

For a full list of customer statuses, refer to [Customer Statuses](additional-details.md#customer-statuses).


{% endstep %}
{% endstepper %}
