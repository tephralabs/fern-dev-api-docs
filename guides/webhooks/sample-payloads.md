# Sample Payloads

Below are example webhook payloads for each event type, illustrating the structure and fields you can expect:

### Customer Created Event:

<pre><code><strong>{
</strong>  "id": "03b7030f-6da1-4d76-9352-cdebd82112c8",
  "apiVersion": "v1",
  "type": "customer.created",
  "createdAt": "2023-01-01T12:00:00Z",
  "resource": {
    "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
    "customerStatus": "CREATED",
    "customerType": "INDIVIDUAL",
    "name": "John Doe",
    "email": "johndoe@example.com",
    "kycLink": "https://kyb.example.com/verify/abc123",
    "updatedAt": "2023-10-01T12:00:00Z",
    "organizationId": "8469411c-48c1-4e26-a032-44688be9cb4b"
  },
  "updatedAt": "2023-01-01T12:00:00Z"
}
</code></pre>

### Customer Updated Event:

```
{
  "id": "03b7030f-1111-4d76-9352-cdebd82112c8",
  "apiVersion": "v1",
  "type": "customer.updated",
  "createdAt": "2023-01-01T12:00:00Z",
  "resource": {
    "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
    "customerStatus": "ACTIVE",
    "customerType": "INDIVIDUAL",
    "name": "John Doe",
    "email": "johndoe@example.com",
    "kycLink": "https://kyb.example.com/verify/abc123",
    "updatedAt": "2023-10-01T12:00:00Z",
    "organizationId": "8469411c-48c1-4e26-a032-44688be9cb4b"
  },
  "updatedAt": "2023-01-01T12:10:00Z"
}
```

### Payment Account Created Event:

```
{
  "id": "03b7030f-1111-1111-9352-cdebd82112c8",
  "apiVersion": "v1",
  "type": "payment_account.created",
  "createdAt": "2023-01-03T10:15:00Z",
  "updatedAt": "2023-01-03T10:15:00Z",
  "resource": {
    "paymentAccountId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
    "paymentAccountType": "EXTERNAL_BANK_ACCOUNT",
    "nickname": "Savings Account",
    "createdAt": "2023-01-03T10:15:00Z",
    "customerId": "c3f1ec5a-4f18-4fd3-bbea-c33ebfceff0a",
    
    // These objects will only be populated if the account of that type existed:
    "externalBankAccount": {
      "bankAccountType": "CHECKING",
      "bankAccountOwnerName": "John Doe",
      "bankAccountOwnerEmail": "john@example.com",
      "bankName": "Chase Bank",
      "bankAccountCurrency": "USD",
      "bankAccountMask": "***6789",
      "bankAccountPaymentMethod": "ACH"
    },
    "externalCryptoWallet": {
      "cryptoWalletType": "EVM",
      "chain": "base",
      "address": "0x1234567890123456789012345678901234567890"
    },
    "fernCryptoWallet": {
      "cryptoWalletType": "EVM",
      "address": "0x1234567890123456789012345678901234567890"
    },
    "bankAccountFormLink": "https://app.fernhq.com/bank_account/abc"
  }
}
```

### Payment Account Deleted Event:

```
{
  "id": "03b7030f-1111-1111-1111-cdebd82112c8",
  "apiVersion": "v1",
  "type": "payment_account.deleted",
  "createdAt": "2023-01-03T10:15:00Z",
  "updatedAt": "2023-01-03T10:20:00Z",
  "resource": {
    "paymentAccountId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
    "paymentAccountType": "EXTERNAL_BANK_ACCOUNT",
    "nickname": "Savings Account",
    "createdAt": "2023-01-03T10:15:00Z",
    "customerId": "c3f1ec5a-4f18-4fd3-bbea-c33ebfceff0a",
    
    // These objects will only be populated if the account of that type existed:
    "externalBankAccount": { ... },
    "externalCryptoWallet": { ... },
    "fernCryptoWallet": { ... }
  }
}
```

### Transaction Created Event:

```
{
  "id": "03b7030f-2222-4d76-8888-1111111111aa",
  "apiVersion": "v1",
  "type": "transaction.created",
  "createdAt": "2023-01-07T09:30:00Z",
  "resource": {
    "transactionId": "1d8beb26-b4d1-47ee-8e5d-0d3905f200c7",
    "transactionStatus": "CREATED",
    "correlationId": "03b7030f-6da1-4e76-9352-3debd82112c8",
    "source": {
      "sourcePaymentAccountId": "072a8b7b-38c7-429a-a6cf-35dae7f2fb77",
      "sourceCurrency": "USD",
      "sourcePaymentMethod": "ACH",
      "sourceAmount": "100.00"
    },
    "destination": {
      "destinationPaymentAccountId": "072a8b7b-38c7-429a-a6cf-35dae7f2fb77",
      "destinationPaymentMethod": "ETHEREUM",
      "destinationCurrency": "USDC",
      "exchangeRate": "1.2",
      "destinationAmount": "100"
    },
    "fees": {
      "feeCurrency": "USD",
      "fernFee": {
        "feeAmount": "5.45",
        "feeUSDAmount": "5.45"
      },
      "developerFee": {
        "feeAmount": "5.45",
        "feeUSDAmount": "5.45"
      }
    },
    "transferInstructions": {
      "type": "fiat",
      "transferPaymentMethod": "ACH",
      "transferMessage": "Payment for order #12345",
      "transferBankName": "First National Bank",
      "transferBankAddress": "123 Bank St, Finance City",
      "transferBankAccountNumber": "987654321",
      "transferRoutingNumber": "123456789",
      "transferBankBeneficiaryName": "John Doe"
    },
    "createdAt": "2023-08-01T12:00:00Z",
    "updatedAt": "2023-08-01T12:00:00Z"
  }
}
```

### Transaction Updated Event:

```
{
  "id": "03b7030f-3333-1111-1111-1111111112c8",
  "apiVersion": "v1",
  "type": "transaction.updated",
  "createdAt": "2023-01-07T09:45:00Z",
  "resource": {
    "transactionId": "1d8beb26-b4d1-47ee-8e5d-0d3905f200c7",
    "transactionStatus": "PROCESSING",
    "correlationId": "03b7030f-6da1-4e76-9352-3debd82112c8",
    "source": {
      "sourcePaymentAccountId": "072a8b7b-38c7-429a-a6cf-35dae7f2fb77",
      "sourceCurrency": "USD",
      "sourcePaymentMethod": "ACH",
      "sourceAmount": "100.00"
    },
    "destination": {
      "destinationPaymentAccountId": "072a8b7b-38c7-429a-a6cf-35dae7f2fb77",
      "destinationPaymentMethod": "ETHEREUM",
      "destinationCurrency": "USDC",
      "exchangeRate": "1.2",
      "destinationAmount": "100"
    },
    "fees": {
      "feeCurrency": "USD",
      "fernFee": {
        "feeAmount": "5.45",
        "feeUSDAmount": "5.45"
      },
      "developerFee": {
        "feeAmount": "5.45",
        "feeUSDAmount": "5.45"
      }
    },
    "transferInstructions": {
      "type": "fiat",
      "transferPaymentMethod": "ACH",
      "transferMessage": "Payment for order #12345",
      "transferBankName": "First National Bank",
      "transferBankAddress": "123 Bank St, Finance City",
      "transferBankAccountNumber": "987654321",
      "transferRoutingNumber": "123456789",
      "transferBankBeneficiaryName": "John Doe"
    },
    "createdAt": "2023-08-01T12:00:00Z",
    "updatedAt": "2023-08-01T12:00:00Z"
  }
}
```

In the transaction examples above, a `transaction.created` event is sent when the transaction is first initiated (with status `CREATED`), and later a `transaction.updated` event may be sent when the status progresses (in this case, to `PROCESSING`; additional `transaction.updated` events would be sent for subsequent status changes like `COMPLETED` or `FAILED`).&#x20;
