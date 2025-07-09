---
description: Use this API to create, update, retrieve, and verify customers.
---

# Customers

Fern supports two ways to verify your customers:

* **Hosted KYC/KYB link** (default): Share a link for your customer to complete verification in Fern's UI.
* **API-based KYC submission**: Submit all required KYC data directly via API using the `kycData` field. This enables a fully branded onboarding experience in your own UI.

{% openapi-operation spec="fern-api" path="/customers" method="post" %}
[OpenAPI fern-api](https://api.fernhq.com/json)
{% endopenapi-operation %}

{% openapi src="https://api.fernhq.com/json" path="/customers/{customerId}" method="get" %}
[https://api.fernhq.com/json](https://api.fernhq.com/json)
{% endopenapi %}

{% openapi src="https://api.fernhq.com/json" path="/customers" method="get" %}
[https://api.fernhq.com/json](https://api.fernhq.com/json)
{% endopenapi %}
