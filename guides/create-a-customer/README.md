# ✅ Create a customer

Customers are your end-users and can be either businesses or individuals. Before they can transact with Fern, each customer must complete a **Know-Your-Customer (KYC)** process. Fern manages this process to ensure secure and compliant fund transfers, giving you confidence in every transaction.

### Step-by-step guide

{% stepper %}
{% step %}
### Create a customer&#x20;

Create either a business or individual customer via the [**Customers API**](../../api-reference/customers/).
{% endstep %}

{% step %}
### Retrieve KYC link

Upon successful customer creation, a hosted KYC link requesting the required information to fill in will be generated. This may be distributed to your end-user for completion, or it can be completed on the customer's behalf by your team.&#x20;

Typically, individuals take 5 minutes to complete the form, and business take 10-15 minutes. ([Sample KYC link](https://app.gitbook.com/o/tqA83i7VnVJ0rFiSNcBO/s/PNfGZh0Pi9izjESrZ2DP/) | [Sample KYB link](https://app.gitbook.com/u/Js7P0SWRFOVcMapukd7GzE5D63w2))&#x20;
{% endstep %}

{% step %}
### Check customer status

The status of the customer will be automatically updated via webhooks. Should additional information be needed from your end-user, we will contact them directly to alleviate any action on your part.

{% hint style="info" %}
If your customer has already been verified by Fern (e.g., they are an existing Fern customer), their status will be automatically updated to **Active**.
{% endhint %}


{% endstep %}
{% endstepper %}

