# First-party offramps

## Overview

Fern's first-party offramps enable your customers to convert any ERC-20 token into a fiat currency, delivering funds to their bank account via local payment rails.

## Step-by-step guide

{% stepper %}
{% step %}
### Create a customer

Use the [Customer API](../api-reference/customers.md) to create your end customer in the Fern system. You must have `customerId` to proceed with the next steps in the process. Your customer does **not** need to be `ACTIVE` to have associated payment accounts and crypto wallets.
{% endstep %}

{% step %}
### Create a payment account for your customer

Once you have the customer's `customerId`, you can create a [payment account](../api-reference/payment-accounts.md) for the customer. For first-party offramps, the destination account is generally an external bank account. You can create an external bank account either by sending the relevant bank account creation details via API or by using a custom-branded hosted bank account form.

**To create a payment account using the bank account form:**

* Create a payment account link using the `POST` endpoint:
  * Set `paymentAccountType` to `EXTERNAL_BANK_ACCOUNT`
  * Use the correct `customerId`
  * Optionally include a `nickname`  for the account if desired
* The response will include a URL you can share with your customer to enter their payment account details.
* Once the customer has entered their payment account details, you will receive a webhook that the payment account has been created (which will include the `paymentAccountId`) or list all payment accounts for the customer to find the specific account and `paymentAccountId` . You will need the `paymentAccountId` for the next steps.

**To create a payment account via API:**

* Create a payment account using the `POST` endpoint:
  * Set `paymentAccountType` to `EXTERNAL_BANK_ACCOUNT`
  * Use the correct `customerId`
  * Optionally include a `nickname`  for the account if desired
  * Include the `externalBankAccount` object with all required fields
* The response will include the `paymentAccountId` that you can use for next steps.
{% endstep %}

{% step %}
### Generate quote for the offramp

Before initiating an offramp transaction, you must create a [quote](../api-reference/quotes.md). The quote returns relevant information such as exchange rate, Fern fees assessed on the transaction, and a `quoteId` that you can use when creating a transaction. To create a quote for your customer, your customer needs to be `ACTIVE`. Quotes expire after 5 minutes. The exchange rates in quotes are not guaranteed and exchange will settle at market rates when a transaction is initiated.

**To create a quote for a first-party offramp:**

* Create a quote using the `POST` endpoint:
  * Include `customerId`&#x20;
  * For the `source` object, include the `sourceCurrency`, `sourcePaymentMethod`, and `sourceAmount`. You do not need to include the `sourcePaymentAccount` for an offramp. If this is an offramp of USDC, you would enter `sourceCurrency` as `USDC` and `sourcePaymentMethod` as the source chain (e.g. `BASE`).
  * For the `destination` object, include the `destinationPaymentAccountId` (which would be the payment account created in step 2), `destinationPaymentMethod` (associated with the payment account in question), and the `destinationCurrency`. The latter two fields can be derived from the payment account itself. If you didn't store this information, you can retrieve the payment account and find the relevant information to include in your quote.
* The quote response will include `quoteId` , estimated exchange rate, the amount expected to receive, and the fees. You'll need `quoteId` for the next step.
{% endstep %}

{% step %}
### Submit transaction

If the quote looks good to you, you can submit a [transaction](../api-reference/transactions/) using the previously generated `quoteId`. Creating a transaction in the Fern API generates a set of transfer instructions. For first-party offramps, these transfer instructions will include a crypto wallet address where you/your customer can transfer funds to initiate the rest of the transaction.
{% endstep %}

{% step %}
### Monitor transaction status

You can track the progress of a transaction in 3 ways:

1. Call the [Transactions API](../api-reference/transactions/#get-transactions-transactionid) endpoint using the `transactionId` to see the most recent status of the transaction
2. Subscribe to [webhooks](webhooks/) for real-time notifications on status changes
3. Refer to the [Developer dashboard](../overview/developer-dashboard.md) to see your customers and their transactions

For a full list of transaction statuses, refer to [Transaction Statuses](../api-reference/transactions/introduction.md#transaction-statuses).


{% endstep %}
{% endstepper %}

