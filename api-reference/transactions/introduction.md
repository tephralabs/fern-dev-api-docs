# Additional details

## Transaction sizes

Each transaction must have a minimum value of **$10**, calculated based on the sending amount. Transactions below this threshold will not be accepted. There is no upper limit on the transaction size.

## Transaction statuses

| Status              | Definition                                                            |
| ------------------- | --------------------------------------------------------------------- |
| `Awaiting_transfer` | Validated and created successfully                                    |
| `Processing`        | Processing is ongoing, funds have been received                       |
| `Completed`         | Successful completion                                                 |
| `Failed`            | Transaction has failed, and funds are returned to the sending address |

## Notifications

Fern notifies customers via email at various steps of the transaction to keep them fully aware of the progress.

**Onramps:**&#x20;

| Status              | Email content                                    |
| ------------------- | ------------------------------------------------ |
| `Awaiting_transfer` | Transfer instructions are sent to the customer   |
| `Processing`        | Funds have been received at the transfer address |
| `Completed`         | Transaction has been completed                   |

**Offramps:**

| Status       | Email content                                    |
| ------------ | ------------------------------------------------ |
| `Processing` | Funds have been received at the transfer address |
| `Completed`  | Transaction has been completed                   |
| `Failed`     | Transaction has failed                           |

Additionally, you can use webhook events to trigger your own notifications, offering in-context guidance and next steps tailored to each customer state. (Coming soon)

## Transaction failures

In rare instances, transactions may fail. When this happens, any received funds will be returned to the sending address, and the transaction will need to be re-initiated. The **GET /transactions** endpoint provides the failure reason, allowing you to address the issue and take corrective action. The failure reason will also be available in the developer dashboard.&#x20;

### Possible failure reasons

This is a non-exhaustive list that will grow over time.&#x20;

<details>

<summary>Onramps</summary>

* **Funds source mismatch** (`funds_source_mismatch`): Incoming funds were sent from a bank account other than the one specified in the quote.
* **Name mismatch** (`name_mismatch`): Funds were sent from a bank account with a name that does not match the customer’s name (only first-party onramps are supported).

</details>

<details>

<summary>Offramps</summary>

* **Price impact/slippage**  (`price_impact_or_slippage`): At the time of execution, the receiving amount was less than the minimum guaranteed amount due to slippage or price impact. Create a new quote and transaction to fetch a new price.&#x20;
* **Returned funds** (`destination_returned_funds`): The destination bank account returned the funds. Reach out to [support@fernhq.com](mailto:support@fernhq.com) for more details.&#x20;
* **Insufficient balance in sending wallet** (`insufficient_balance`): Ensure that there is sufficient balance in the sending wallet before initiating a subsequent offramp.&#x20;
* **Missing chain ID** (`missing_chain_id`): The transaction had failed because the chain ID was not specified.  Create a new quote and transaction to resolve.
* **Missing minimum guaranteed amount** (`missing_minimum_guaranteed`): The transaction had failed because the minimum guaranteed amount was not specified.  Create a new quote and transaction to resolve.

</details>





