---
description: >-
  The Fern API has several core concepts. Here, we describe these concepts at a
  high-level and how they relate to each other.
---

# Core API concepts

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Fern's core API concepts are:

* [**Customers**](../api-reference/customers/) — Using the Fern API, you can create your individual and business users as customers. Customers are uniquely identified by email address. Customers must be verified in order to use Fern quotes and transactions.
* [**Payment Accounts**](../api-reference/payment-accounts.md) — You can create bank accounts for your customers. Depending on the [fiat currency and payment method](broken-reference) of the account, you may need to collect different information about the customer's bank account to successfully create the account and link it to the customer. You must create a bank account for a customer before you can initiate a first-party on/offramp with that bank account.
* [**Quotes**](../api-reference/quotes.md) — Before you can create a transaction, you must create a quote. Quotes enable you to specify important information about the transaction, such as the source and destination currencies and accounts and your developer fees. Quotes also provide useful information that you can use to display in your application, such as exchange rates and total fees.
  * You must create a quote before you create a transaction, and you will use the quote ID when creating a transaction.
  * Quotes can only be created for verified customers.
  * Quotes expire after 5 minutes.
  * Once you create a transaction with a quote, that quote can no longer be used. To generate a new transaction, you will have to create a new quote.
* [**Transactions**](../api-reference/transactions/) — Transactions execute currency conversions. You create transactions using quote IDs.
* **Contacts (coming soon)** — Contacts are third parties that your customers transact with, and can have registered bank accounts and wallets.
