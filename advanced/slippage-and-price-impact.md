# ✅ Slippage & Price Impact

Converting ERC-20 tokens, particularly those with smaller market caps and volatile prices, can lead to significant price impact, especially for crypto-to-fiat flows.

To protect your customers and ensure competitive pricing, we offer a **minimum guaranteed amount** for each transaction (see **Transactions API**). If this amount cannot be achieved at the time of execution, the transaction will be canceled, and the funds will be returned to the customer's account.

The **XXX webhook** will provide the reason for the failure, allowing you to take appropriate action. You can retry the offramp when market conditions improve.
