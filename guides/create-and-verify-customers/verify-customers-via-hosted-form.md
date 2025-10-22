# Verify customers via hosted form

## Overview

Fern has hosted forms that simplify the KYC/KYB data collection for your Customers. We often see our partners use Fern hosted forms during initial implementation to get out an MVP of the experience, and then transitioning to building their own KYC/B forms over time. This guide shows you how to use the Fern hosted forms to verify your customers.

For a fully branded onboarding experience, see [Verify customers via API](api-verification.md) for details.

## Step-by-step guide

{% stepper %}
{% step %}
#### Create a customer

Create either a business or individual customer via the [Customers API](../../api-reference/customers.md). If the customer creation call succeeds, the response will include a hosted KYC/KYB link that you can share with your customer directly. Customer status will be `CREATED` .

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
    "customerId": "5be4866c-4d85-4861-903d-a6295dfdf1e1",
    "customerStatus": "created",
    "email": "bob@builder.com",
    "customerType": "INDIVIDUAL",
    "name": "Bob The Builder",
    "kycLink": "https://app.fernhq.com/verify-customer/5be4866c-4d85-4861-903d-a6295dfdf1e1",
    "updatedAt": "2025-04-29T13:31:25.954Z",
    "organizationId": "73fc5722-ae38-4530-a45f-db2df8f69810"
}
```
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
#### Customer completes verification via KYC link

You can share the hosted KYC form link directly with your end user for completion, or your team can complete the form on your customer's behalf. Once the Customer completes the form, their status will change from `CREATED` to `UNDER_REVIEW` .
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
    "kycLink": "https://app.fernhq.com/verify-customer/5be4866c-4d85-4861-903d-a6295dfdf1e1",
    "updatedAt": "2025-04-29T16:43:09.261Z",
    "organizationId": "73fc5722-ae38-4530-a45f-db2df8f69810"
}
```
{% endtab %}
{% endtabs %}

If any additional information is needed to verify your customer, the Fern compliance team will contact them directly to minimize compliance overhead for your team. Once the customer is fully approved, their status will change to `ACTIVE`. For a full list of customer statuses, refer to [Customer Statuses](additional-details.md#customer-statuses).
{% endstep %}
{% endstepper %}
