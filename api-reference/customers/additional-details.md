# Additional details

### Customer statuses

| Status        | Definition                                                                                                                                                                                                                                                           |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Created`     | Customer object has been created.                                                                                                                                                                                                                                    |
| `Pending`     | <p>Customer is pending verification. This encompasses all interim states during the verification process. <br><br>This includes cases where the KYC link is incomplete, verification is in progress, or additional information has been requested from the user.</p> |
| `Active`      | Customer is approved and ready to transact.                                                                                                                                                                                                                          |
| `Rejected`    | Customer is rejected. The webhook will provide a list of rejection reasons (if available).                                                                                                                                                                           |
| `Deactivated` | Customer has been deactivated by the developer.                                                                                                                                                                                                                      |

### Notifications

Fern notifies customers via email at each step of the verification process, handling the entire process on your behalf.

Additionally, you can use webhook events to trigger your own notifications, offering in-context guidance and next steps tailored to each customer state.
