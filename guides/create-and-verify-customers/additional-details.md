# Additional details

## Customer Statuses

| Status         | Definition                                               |
| -------------- | -------------------------------------------------------- |
| `CREATED`      | Initial status when customer is created without KYC data |
| `UNDER_REVIEW` | KYC data submitted and being verified by providers       |
| `ACTIVE`       | Verification approved, customer ready to transact        |
| `REJECTED`     | Verification rejected by providers                       |

**Notes:**

* Crypto-to-crypto transactions are available for all customer statuses. Fiat-related services require `ACTIVE` status.
* Core customer information like first name, last name, and email address cannot be updated once set.

## Notifications

Fern notifies customers via email at each step of the verification process, handling the entire process on your behalf. For customers who wish to communicate with their customers directly about additional information that is needed, we can work with you to send RFIs to you and you can coordinate with your customer.
