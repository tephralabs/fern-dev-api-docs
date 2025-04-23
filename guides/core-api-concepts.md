---
description: >-
  The Fern API has several core concepts. Here, we describe these concepts at a
  high-level and how they relate to each other.
---

# Core API concepts

<figure><img src="../.gitbook/assets/Screenshot 2025-04-23 at 10.52.31 AM.png" alt=""><figcaption></figcaption></figure>

Fern's core API concepts are:

* [**Customers**](../api-reference/customers.md) — Using the Fern API, you can create your individual and business users as customers. Customers are uniquely identified by email address. Customers must be verified in order to use Fern quotes and transactions.
* [**Payment Accounts**](../api-reference/payment-accounts.md) — You can create payment accounts for your customers. Depending on the [fiat currency and payment method](../fiat-currency-support.md) of the account, you may need to collect different information about the customer's account to successfully link it to the customer. Once a payment account has been successfully created, you can initiate transactions with that account.&#x20;
* [**Quotes**](../api-reference/quotes.md) — Quotes enable you to specify critical details about a transaction. You receive exchange rates and other information as part of the API response.&#x20;
  * You must create a quote before you create a transaction, and you will use the quote ID when creating a transaction.
  * Quotes can only be created for approved customers.
  * Quotes expire after 5 minutes.
  * Once you create a transaction with a quote, that quote can no longer be used. To generate a new transaction, you will have to create a new quote.
* [**Transactions**](../api-reference/transactions/) — Transactions confirm the details of a quote. You create transactions using quote IDs.
  * Transaction API responses include instructions that provide details on where your customer should transfer funds in order to proceed with the next steps of the transaction.
* **Contacts (coming soon)** — Contacts are third parties that your customers transact with, and can have registered payment accounts.
