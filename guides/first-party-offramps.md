# First-party offramps

### Overview

Fern's first-party offramps enable your customers to convert any ERC-20 into fiat, delivering funds to their bank account via local payment rails. &#x20;

To implement offramps, use the **Quotes API** and [**Transactions API**](../api-reference/transactions/). Specify a cryptocurrency as the source and a fiat currency as the destination to configure the transaction as an offramp.

For the latest list of supported fiat and cryptocurrencies, as well as payment methods, refer to the [**Supported Coverage**](../overview/what-fern-supports/) section.

### Step-by-step guide

{% stepper %}
{% step %}
### Add a bank account to an existing customer

Use the [**Bank Accounts**](../group-1/bank-accounts.md) endpoint to create a new bank account.&#x20;

{% hint style="info" %}
Ensure that the beneficiary name and account type matches the customer's details. Otherwise, the transaction will fail and funds will not be transferred.&#x20;
{% endhint %}
{% endstep %}

{% step %}
### Generate quote

To fetch a proposed price for a currency conversion, use the **Quotes** endpoint. This endpoint generates a quote for your specified currency route, guaranteeing the price for **5 min**. Quotes provide transparent details, including:

* the exact receiving amount,&#x20;
* fees,&#x20;
* minimum guaranteed amount,
* price impact (if applicable).

For more details, visit the **Quotes** section.

```
// Perhaps some code on how to generate quote?
```
{% endstep %}

{% step %}
### Submit transaction

To create a transaction, use the **quoteID** to generate an order for fulfillment. Once the transaction is initiated and funds are received at the target address, the destination currency is sent to the specified destination address via the indicated payment rail.&#x20;

For more details, check out the [**Transactions**](../api-reference/transactions/) section.

```
// Perhaps some code on how to submit transaction?
```
{% endstep %}

{% step %}
### Track transaction status

Track the progress of a transaction by calling the **XXX endpoint** or visiting the **Developer Dashboard**. You can also subscribe to webhooks for real-time notifications on status changes (coming soon).

For a full list of transaction statuses, refer to the **Transaction Statuses Documentation**.

```
// Perhaps some code on how to track transaction status?
```
{% endstep %}
{% endstepper %}

