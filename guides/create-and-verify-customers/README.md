# Create and verify customers

## Overview

Customers are your end users. Fern supports businesses and individuals as end users. Before your customer can transact with Fern, they must complete a customer verification process, i.e. **Know Your Customer (KYC) or Know Your Business (KYB)**. Fern manages this process to ensure secure and compliant funds transfers, giving you confidence in every transaction.

## Step-by-step guide

{% stepper %}
{% step %}
### Create a customer&#x20;

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
### Customer completes verification via KYC link

You can share the hosted KYC form link directly with your end user for completion, or your team can complete the form on your customer's behalf. Typically, it takes around 3 minutes for individuals to complete the form, and around 10-15 minutes for businesses. Once the customer completes the form, their status will change from `CREATED` to `PENDING` .
{% endstep %}

{% step %}
### Get customer status

You can use the [Customers API](../../api-reference/customers.md) `GET` endpoints to check for updates to the customer status. Status updates will also be communicated via webhooks.

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

{% hint style="info" %}
If your customer has already been verified by Fern (e.g. they are an existing Fern customer), their status will automatically update from `CREATED` to `ACTIVE`.
{% endhint %}


{% endstep %}
{% endstepper %}

