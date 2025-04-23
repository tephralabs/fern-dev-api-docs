# Customer statuses

### Customer statuses

| Status        | Definition                                                                                                                                                                                      |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `created`     | Customer object has been created.                                                                                                                                                               |
| `pending`     | <p>Customer is pending verification. <br><br>This includes cases where the KYC link is incomplete, verification is in progress, or additional information has been requested from the user.</p> |
| `active`      | Customer is approved and ready to transact.                                                                                                                                                     |
| `rejected`    | Customer is rejected. The webhook will provide a list of rejection reasons.                                                                                                                     |
| `deactivated` | Customer has been deactivated.                                                                                                                                                                  |

### Notifications

Fern notifies customers via email at each step of the verification process, handling the entire process on your behalf.

Additionally, you can use webhook events to trigger your own notifications, offering in-context guidance and next steps tailored to each customer state. (Coming soon)
