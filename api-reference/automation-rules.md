# Automation Rules

Automation Rules let you streamline how funds move from your Fern wallets to your bank account. Instead of manually off-ramping, you can set up rules that automatically convert and transfer funds based on conditions you define.

Typical use cases include:

* Scheduled Cash-outs: Set a recurring schedule (daily, weekly, etc.) to automatically convert and transfer funds.
* Threshold-based Sweeps: Automatically off-ramp once your wallet balance reaches a set amount.

{% openapi-operation spec="fern-api" path="/automation_rules" method="post" %}
[OpenAPI fern-api](https://api.fernhq.com/json)
{% endopenapi-operation %}

{% openapi-operation spec="fern-api" path="/automation_rules" method="get" %}
[OpenAPI fern-api](https://api.fernhq.com/json)
{% endopenapi-operation %}

{% openapi-operation spec="fern-api" path="/automation_rules/{ruleId}" method="get" %}
[OpenAPI fern-api](https://api.fernhq.com/json)
{% endopenapi-operation %}

{% openapi-operation spec="fern-api" path="/automation_rules/{ruleId}" method="put" %}
[OpenAPI fern-api](https://api.fernhq.com/json)
{% endopenapi-operation %}

{% openapi-operation spec="fern-api" path="/automation_rules/{ruleId}" method="delete" %}
[OpenAPI fern-api](https://api.fernhq.com/json)
{% endopenapi-operation %}
