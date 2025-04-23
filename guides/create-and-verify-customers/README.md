# Create and verify customers

## Overview

Customers are your end users and can be either businesses or individuals. Before they can transact with Fern, each customer must complete a customer verification process, i.e. **Know Your Customer (KYC) or Know Your Business (KYB)**. Fern manages this process to ensure secure and compliant funds transfers, giving you confidence in every transaction.

## Step-by-step guide

{% stepper %}
{% step %}
### Create a customer&#x20;

Create either a business or individual customer via the [**Customers API**](../../api-reference/customers.md).
{% endstep %}

{% step %}
### Customer completes verification via KYC link

Upon successful customer creation, a hosted verification link will be generated for that customer. The link may be directly shared with your end user for completion, or your team can complete the form on your customer's behalf.&#x20;

Typically, it takes around 3 minutes for individuals to complete the form, and around 10-15 minutes for businesses.&#x20;

<figure><img src="../../.gitbook/assets/KYC link-1.png" alt=""><figcaption><p>Preview of the hosted KYC link that will be generated. {Company} will be replaced with your business name.</p></figcaption></figure>
{% endstep %}

{% step %}
### Check customer status

The status of the customer will be automatically updated (webhooks coming soon). If any additional information is needed to verify your customer, the Fern compliance team will contact them directly to minimize compliance overhead for your team.

For a full list of customer statuses, refer to [**Customer Statuses**](additional-details.md#customer-statuses).

{% hint style="info" %}
If your customer has already been verified by Fern (e.g. they are an existing Fern customer), their status will automatically update from **Created** to **Active**.
{% endhint %}


{% endstep %}
{% endstepper %}

