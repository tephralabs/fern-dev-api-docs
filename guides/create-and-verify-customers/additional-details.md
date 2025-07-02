# Additional Details

## Document Types

- **GOVERNMENT_ID:** PASSPORT, DRIVERS_LICENSE, NATIONAL_ID
- **PROOF_OF_ADDRESS:** UTILITY_BILL, BANK_STATEMENT, RENTAL_AGREEMENT

## Customer Statuses

| Status                         | Definition                                                              | Access Level                                                 |
| ------------------------------ | ----------------------------------------------------------------------- | ------------------------------------------------------------ |
| `CREATED`                      | Initial status when customer is created without KYC data                | Wallet and payment account creation                          |
| `UNDER_REVIEW`                 | KYC data submitted and being verified by providers                      | Wallet and payment account creation                          |
| `NEEDS_ADDITIONAL_INFORMATION` | Additional documentation required to complete verification              | Wallet and payment account creation                          |
| `ACTIVE`                       | Verification approved, customer ready to transact                       | All services including quotes/transactions and fiat accounts |
| `REJECTED`                     | Verification rejected by providers (webhook includes rejection reasons) | No access to services                                        |
| `DEACTIVATED`                  | Manually deactivated by customer, provider, or Fern                     | No access to services                                        |

## API-Based KYC Process

1. Submit KYC data via API or form → Status: `UNDER_REVIEW`
2. If additional info needed → Status: `NEEDS_ADDITIONAL_INFORMATION`
3. Final outcome:
   - Approved → Status: `ACTIVE`
   - Rejected → Status: `REJECTED`

You can monitor status changes via API or webhooks.

## Notifications

Fern notifies customers via email at each step of the verification process, handling the entire process on your behalf.

Additionally, you can use webhook events to trigger your own notifications, offering in-context guidance and next steps tailored to each customer state. (Coming soon)
