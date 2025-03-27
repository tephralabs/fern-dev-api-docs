---
description: >-
  Use this API to create a customer, update their details, and get their
  details.
---

# Customers

Upon successful customer creation, a hosted KYC link requesting the required information to fill in will be generated. This may be distributed to your end-user for completion, or it can be completed on the customer's behalf by your team.&#x20;

{% openapi src="https://api.fernhq.com/json" path="/customers" method="post" %}
[https://api.fernhq.com/json](https://api.fernhq.com/json)
{% endopenapi %}

{% openapi src="https://api.fernhq.com/json" path="/customers/{customerId}" method="get" %}
[https://api.fernhq.com/json](https://api.fernhq.com/json)
{% endopenapi %}

{% openapi src="https://api.fernhq.com/json" path="/customers" method="get" %}
[https://api.fernhq.com/json](https://api.fernhq.com/json)
{% endopenapi %}



