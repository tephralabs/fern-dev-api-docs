# Additional Details

## Document Types

- **GOVERNMENT_ID:** PASSPORT, DRIVERS_LICENSE, NATIONAL_ID
- **PROOF_OF_ADDRESS:** UTILITY_BILL, BANK_STATEMENT, RENTAL_AGREEMENT

## Customer Statuses

| Status        | Definition                                                                                                                                                                                      |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `CREATED`     | Customer object has been created.                                                                                                                                                               |
| `UNDER_REVIEW`| KYC/KYB data has been submitted (via API or form) and is under review by Fern and/or verification providers.                                                                                    |
| `NEEDS_ADDITIONAL_INFORMATION` | Additional information or documents are required to complete verification. Customer retains limited access until requirements are met.                                         |
| `ACTIVE`      | Customer is approved and ready to transact.                                                                                                                                                     |
| `REJECTED`    | Customer is rejected. The webhook will provide a list of rejection reasons.                                                                                                                     |
| `DEACTIVATED` | Customer has been deactivated.                                                                                                                                                                  |

## Status Transitions in API-based KYC Flow

- When you submit KYC data via API or form, the customer status will be `UNDER_REVIEW` while verification is in progress.
- If more information is needed, the status will change to `NEEDS_ADDITIONAL_INFORMATION`.
- Once approved, the status will be `ACTIVE`. If rejected, it will be `REJECTED`.
- You can monitor status via API or webhooks.

## Notifications

Fern notifies customers via email at each step of the verification process, handling the entire process on your behalf.

Additionally, you can use webhook events to trigger your own notifications, offering in-context guidance and next steps tailored to each customer state. (Coming soon)
