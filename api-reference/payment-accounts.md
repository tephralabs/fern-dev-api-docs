---
description: Use this API to add, update and get payment accounts for an existing customer.
---

# Payment accounts

{% openapi src="https://api.fernhq.com/json" path="/payment-accounts" method="post" %}
[https://api.fernhq.com/json](https://api.fernhq.com/json)
{% endopenapi %}

{% openapi src="https://api.fernhq.com/json" path="/payment-accounts/{paymentAccountId}" method="get" %}
[https://api.fernhq.com/json](https://api.fernhq.com/json)
{% endopenapi %}

{% openapi src="https://api.fernhq.com/json" path="/payment-accounts" method="get" %}
[https://api.fernhq.com/json](https://api.fernhq.com/json)
{% endopenapi %}

{% openapi-operation spec="fern-api" path="/payment-accounts/{paymentAccountId}/balance" method="get" %}
[OpenAPI fern-api](https://api.fernhq.com/json)
{% endopenapi-operation %}

{% openapi-operation spec="fern-api" path="/payment-accounts/{paymentAccountId}" method="delete" %}
[OpenAPI fern-api](https://api.fernhq.com/json)
{% endopenapi-operation %}
