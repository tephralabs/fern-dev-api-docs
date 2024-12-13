# ✅ First-party onramps

### Overview

<mark style="color:red;">Insert diagram</mark>

Fern's first-party onramps enable your customers to convert fiat into crypto, delivering funds directly to their wallets—whether external (e.g. Metamask) or an embedded wallet (e.g. Privy). The onramps are accessible via API, allowing you to design a seamless and customized user experience.

To implement onramps, use the **Quotes API** and **Transactions API**. Specify a fiat currency as the source and a cryptocurrency as the destination to configure the transaction as an onramp.

For the latest list of supported fiat and cryptocurrencies, as well as payment methods, refer to the [**Supported Coverage**](../overview/supported-regions-and-currencies.md) section.

### Step-by-step guide

{% stepper %}
{% step %}
### Add a bank account to an existing customer

Use the [**Bank Accounts**](../group-1/bank-accounts.md) endpoint to create a new bank account.&#x20;

{% hint style="info" %}
Ensure that the beneficiary name and account type matches the customer details. Otherwise, the funds will be returned to the sending bank account.&#x20;
{% endhint %}
{% endstep %}

{% step %}
### Generate quote

To fetch a proposed price for a currency conversion, use the **Quotes** endpoint. This endpoint generates a quote for your specified currency route, guaranteeing the price for **X hours**. Quotes provide transparent details, including the exact receiving amount and any applicable fees.

```
// Perhaps some code on how to generate quotes?
```
{% endstep %}

{% step %}
### Submit transaction

To create a transaction, use the **quoteID** to generate an order for fulfillment. Once the transaction is initiated and funds are received at the target address, the destination currency is sent to the specified destination address, completing the transaction within seconds or minutes.

For more details, check out the [**Transactions**](../api-reference/transactions/) section.

```
// Perhaps some code on how to submit transaction?
```
{% endstep %}

{% step %}
### Track transaction status

Track the progress of a transaction by calling the **XXX endpoint** or visiting the **Developer Dashboard**. To stay updated, you can also subscribe to **Webhooks** for real-time notifications on status changes.

For a full list of transaction statuses, refer to the **Transaction Statuses Documentation**.

```
// Perhaps some code on how to track transaction status?
```
{% endstep %}
{% endstepper %}

