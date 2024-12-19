---
description: >-
  Use this API to create a customer, update their details, and get their
  details.
---

# Customers

## Create a customer

Upon successful customer creation, a hosted KYC link requesting the required information to fill in will be generated. This may be distributed to your end-user for completion, or it can be completed on the customer's behalf by your team.&#x20;

{% swagger src="../../.gitbook/assets/api.json" path="/api/v0/customers" method="post" %}
[api.json](../../.gitbook/assets/api.json)
{% endswagger %}

## Get customer details

{% swagger src="../../.gitbook/assets/api.json" path="/api/v0/customers/{customerId}" method="get" %}
[api.json](../../.gitbook/assets/api.json)
{% endswagger %}

