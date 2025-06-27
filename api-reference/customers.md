---
description: >-
  Use this API to create, update, retrieve, and verify customers.
---

# Customers

Fern supports two ways to verify your customers:
- **Hosted KYC/KYB link** (default): Share a link for your customer to complete verification in Fern's UI.
- **API-based KYC submission**: Submit all required KYC data directly via API using the `kycData` field. This enables a fully branded onboarding experience in your own UI.

{% openapi src="https://dev-api-gateway-svc.onrender.com/json" path="/customers" method="post" %}
[https://dev-api-gateway-svc.onrender.com/json](https://dev-api-gateway-svc.onrender.com/json)
{% endopenapi %}

{% openapi src="https://api.fernhq.com/json" path="/customers/{customerId}" method="get" %}
[https://api.fernhq.com/json](https://api.fernhq.com/json)
{% endopenapi %}

{% openapi src="https://api.fernhq.com/json" path="/customers" method="get" %}
[https://api.fernhq.com/json](https://api.fernhq.com/json)
{% endopenapi %}



