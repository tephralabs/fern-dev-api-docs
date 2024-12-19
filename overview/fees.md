# Fees

Fern fees include a developer subscription fee and transaction fees. The per-transaction fee gives developers an opportunity to earn revenue on each transaction facilitated by your platform.

## Subscription fee

When signing up for Fern, there is no contract, term commitment, or monthly contract minimum. Instead, Fern charges a flat subscription fee. Fern will provide a monthly invoice to remit payment.

## Transaction fees

Fern enables developers to earn fees from each conversion transaction. Fees are deducted from the receiving amount before being sent to the destination account. The total transaction fee that is deducted includes:

* Your developer fee (if any)
* Fern conversion fee
* Fiat payment method fee
* Gas reimbursement fee

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### Your developer fee

When creating [Quotes](../api-reference/quotes.md) for your customer's transaction, you can also include a developer fee to charge additional to the Fern fees. Fern will deduct these fees for you, and remit the aggregated sum to you on a monthly basis.

### Fern conversion fee

Fern charges a 0.5% fee for all conversion transactions.

### Fiat payment method fee

If a transaction is settling on fiat payment rails (e.g. offramps), Fern charges a fiat payment method fee. These fees vary by fiat currency and payment method.

| Currency                   | Payment method | Fiat fee |
| -------------------------- | -------------- | -------- |
| :flag\_us: US Dollar (USD) | ACH            | $1       |
|                            | Same-day ACH   | $5       |
|                            | Wire           | $20      |

### Gas reimbursement fee

Gas fees on Ethereum mainnet have tended to be increasingly non-trivial. Therefore, we include an estimated gas fee for any transactions that will require Fern to conduct a transfer on Ethereum mainnet. Gas fees on other supported networks are sponsored by Fern.

