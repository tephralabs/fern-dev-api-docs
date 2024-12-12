# ✅ Customer creation

Customers are your end-users, and can be either businesses or individuals. &#x20;

* Once you create a customer, they are required to undergo a Know-Your-Customer (KYC) process before they can start transacting with Fern.&#x20;
* Fern conducts the KYC processes to enable you to transfer funds with confidence.&#x20;

{% stepper %}
{% step %}
### Create a customer&#x20;

Create either a business or individual customer via the `Customers`API.&#x20;
{% endstep %}

{% step %}
### Retrieve KYC link

Upon successful customer creation, a hosted KYC link requesting the required information to fill in will be generated. This may be distributed to your end-user for completion, or it can be completed on the customer's behalf by your team.&#x20;

Typically, individuals take 5 minutes to complete the form, and business take 10-15 minutes.
{% endstep %}

{% step %}
### Check customer status

The status of the customer will be automatically updated via webhooks. Should additional information be needed from your end-user, we will contact them directly to alleviate any action on your part.
{% endstep %}
{% endstepper %}

