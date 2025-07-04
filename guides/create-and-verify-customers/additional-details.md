# Additional Details

## Document Types

- **GOVERNMENT_ID:** PASSPORT, DRIVERS_LICENSE, NATIONAL_ID
- **PROOF_OF_ADDRESS:** UTILITY_BILL, BANK_STATEMENT, RENTAL_AGREEMENT

## Customer Statuses

| Status                         | Definition                                                              |
| ------------------------------ | ----------------------------------------------------------------------- |
| `CREATED`                      | Initial status when customer is created without KYC data                |
| `UNDER_REVIEW`                 | KYC data submitted and being verified by providers                      |
| `NEEDS_ADDITIONAL_INFORMATION` | Additional documentation required to complete verification              |
| `ACTIVE`                       | Verification approved, customer ready to transact                       |
| `REJECTED`                     | Verification rejected by providers (webhook includes rejection reasons) |
| `DEACTIVATED`                  | Manually deactivated by customer, provider, or Fern                     |

**Notes:**

- Crypto-to-crypto transactions are available for all customer statuses except `DEACTIVATED`. Fiat-related services require `ACTIVE` status.
- Core customer information like first name, last name, and email address cannot be updated once set.

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
