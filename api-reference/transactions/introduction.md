# Additional details

## Transaction sizes

Each transaction must have a minimum value of **$10**, calculated based on the sending amount. Transactions below this threshold will not be accepted. There is no upper limit on the transaction size.

## Transaction statuses

| Status       | Definition                                                              |
| ------------ | ----------------------------------------------------------------------- |
| `Created`    |  Validated and created successfully.                                    |
| `Processing` | Processing is ongoing.                                                  |
| `Completed`  | Successful completion.                                                  |
| `Failed`     | Transaction has failed, and funds are returned to the sending address.  |

## Notifications

Fern notifies customers via email at <mark style="color:red;">all steps</mark> of the transaction (except `Processing`) to keep them fully aware of the progress.

Additionally, you can use webhook events to trigger your own notifications, offering in-context guidance and next steps tailored to each customer state.

## Transaction failures

In rare instances, transactions may fail. When this happens, any received funds will be returned to the sending address, and the transaction will need to be re-initiated. The **GET /transactions** endpoint provides the failure reason, allowing you to address the issue and take corrective action.

### Possible failure reasons

This is a non-exhaustive list that will grow over time.&#x20;

<details>

<summary>Onramps</summary>

* **Funds source mismatch**: Incoming funds were sent from a bank account other than the one specified in the quote.
* **Name mismatch**: Funds were sent from a bank account with a name that does not match the customer’s name (only first-party onramps are supported).

</details>

<details>

<summary>Offramps</summary>

* **Price impact/slippage**: At the time of execution, the receiving amount was less than the minimum guaranteed amount due to slippage or price impact.
* **Invalid destination bank account**: The specified destination bank account is not valid.
* **Returned funds**: The destination bank account returned the funds.

</details>





