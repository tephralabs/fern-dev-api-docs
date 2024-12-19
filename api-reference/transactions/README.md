---
description: >-
  Use this API to create and get transactions for a customer and a quote.
  Requires the creation of a quote beforehand per transaction.
---

# Transactions

## Create a transaction

Creates a transaction using a given quoteID. QuoteID must be valid and not expired.&#x20;

{% swagger src="../../.gitbook/assets/api.json" path="/api/v0/transactions" method="post" %}
[api.json](../../.gitbook/assets/api.json)
{% endswagger %}

## Get transaction details

{% swagger src="../../.gitbook/assets/api.json" path="/api/v0/transactions/{transactionId}" method="get" %}
[api.json](../../.gitbook/assets/api.json)
{% endswagger %}

