---
description: >-
  Use this API to create and fetch quotes for transactions. Currently, onramp
  and offramp transaction types are supported and will be inferred from the
  source and destination.
---

# Quotes

## Create a quote

Creates a new quote for a transaction. This endpoint generates a quote for your specified currency route, guaranteeing the price for 5 min.&#x20;

{% swagger src="../.gitbook/assets/api.json" path="/api/v0/quotes" method="post" %}
[api.json](../.gitbook/assets/api.json)
{% endswagger %}

## Get quote details

{% swagger src="../.gitbook/assets/api.json" path="/api/v0/quotes/{quoteId}" method="get" %}
[api.json](../.gitbook/assets/api.json)
{% endswagger %}

