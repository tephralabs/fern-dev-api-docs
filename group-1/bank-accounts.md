---
description: >-
  Use this API to add, update and get bank accounts for an existing customer.
  Currently, only USD bank accounts with domestic ACH/wire routing numbers are
  supported.
---

# Bank accounts

## Create a bank account

{% swagger src="../.gitbook/assets/api.json" path="/api/v0/bank-accounts" method="post" %}
[api.json](../.gitbook/assets/api.json)
{% endswagger %}

## Update bank account details

{% swagger src="../.gitbook/assets/api.json" path="/api/v0/bank-accounts/{bankAccountId}" method="patch" %}
[api.json](../.gitbook/assets/api.json)
{% endswagger %}

## Get bank  account details

{% swagger src="../.gitbook/assets/api.json" path="/api/v0/bank-accounts/{bankAccountId}" method="get" %}
[api.json](../.gitbook/assets/api.json)
{% endswagger %}

## List bank accounts

{% swagger src="../.gitbook/assets/api.json" path="/api/v0/bank-accounts" method="get" %}
[api.json](../.gitbook/assets/api.json)
{% endswagger %}

