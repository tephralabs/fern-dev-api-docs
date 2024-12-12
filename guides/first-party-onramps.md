# First-party onramps

Fern's first-party onramps converts between fiat to crypto to your customers' wallets (e.g. their own external wallets, or an embedded wallet you have created for them). Currently, Fern onramps are available via API, so that you can create your desired user experience within your app or service.&#x20;

You can create onramps using the `Quotes` and `Transactions` APIs. Use a fiat currency as the transaction source, and a crypto currency as the destination, to specify the transaction as an onramp.&#x20;

For the latest list of supported fiat and crypto currencies and payment methods, please refer to [Supported coverage](../overview/supported-regions-and-currencies.md).

### Step-by-step guide

{% stepper %}
{% step %}
### Generate quote

Generate a quote for your currency route to fetch the proposed price for the conversion, using the `Quotes`endpoint. Quotes guarantee prices for X hours, and transparently communicate the receiving amount and fees charged. Learn more about Quotes[^1] here.&#x20;

```
// Perhaps some code on how to generate quote?
```
{% endstep %}

{% step %}
### Submit transaction

Creating a transaction using the quoteID generates an order to be fulfilled. Once the transaction is created and funds are received at the [target address](#user-content-fn-2)[^2], the destination currency will be sent to the destination address to complete the transaction, within seconds or minutes. Learn more about Transactions here.&#x20;

```
// Perhaps some code on how to submit transaction?
```
{% endstep %}

{% step %}
### Track transaction status

&#x20;You can check on the transaction progress by calling the XXX endpoint, or visiting the developer dashboard. You can also subscribe to Webhooks to notify you of status changes. Find the complete list of transaction statuses here.&#x20;

```
// Perhaps some code on how to track transaction status?
```
{% endstep %}
{% endstepper %}

### First-party only

For onramps, the source account has to belong to the customer. e.g. to onramp to John Doe's wallet, the source bank account where the fiat comes from needs to belong to John Doe. If the names of the accounts do not match, funds will be returned to the sourced account, and the onramp will not proceed.&#x20;

[^1]: to link to Quotes Introduction page

[^2]: what is a better name? what name are we using in the code?

