# Nigeria

## General information

| Field                        | Value                                                                                                               |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Supported payment rails      | NIBSS, NEFT                                                                                                         |
| Settlement time              | Real-time                                                                                                           |
| Supported transaction types  | <ul><li>First-party payin</li><li>First-party payout</li><li>Third-party payin</li><li>Third-party payout</li></ul> |
| Supported for customer types | Business, Individual                                                                                                |
| 2-digit ISO country code     | `NG`                                                                                                                |

## Payment account creation

Payment account creation requires:

* `bankName` must match one of the bank names referenced below
* `bankAccountCurrency` : `NGN`
* `bankAccountPaymentMethod` : `NG_NIBSS_NEFT`
* `nubanNumber`



Sample payment account creation request:

```
{
  "paymentAccountType": "EXTERNAL_BANK_ACCOUNT",
  "customerId": "072a8b7b-38c7-429a-a6cf-35dae7f2fb77",
  "externalBankAccount": {
    "bankName": "First City Monument Bank PLC",
    "bankAccountCurrency": "NGN",
    "bankAddress": {
      "country": "NG",
      "addressLine1": "Primrose Tower",
      "addressLine2": "17A Tinubu Street",
      "city": "Marina",
      "state": "Lagos",
      "postalCode": "102273",
      "locale": "en-US"
    },
    "bankAccountType": "CHECKING",
    "bankAccountPaymentMethod": "NG_NIBSS_NEFT",
    "bankAccountOwner": {
      "email": "john@example.com",
      "firstName": "John",
      "lastName": "Doe",
      "address": {
        "country": "US",
        "addressLine1": "350 5th Avenue",
        "addressLine2": "Floor 21",
        "city": "New York",
        "state": "NY",
        "postalCode": "10016",
        "locale": "en-US"
      },
      "type": "INDIVIDUAL"
    },
    "nubanNumber": "123456789012"
  },
  "isThirdParty": false
}
```

### Accepted bank names

| Bank name                     | Account owner type |
| ----------------------------- | ------------------ |
| First City Monument Bank PLC  | INDIVIDUAL         |
| Afribank Nigeria Plc          | INDIVIDUAL         |
| Access Bank PLC               | INDIVIDUAL         |
| CITI Bank                     | INDIVIDUAL         |
| Fidelity Bank PLC             | INDIVIDUAL         |
| Enterprise Bank Limited       | INDIVIDUAL         |
| First Bank Of Nigeria PLC     | INDIVIDUAL         |
| Ecobank Nigeria PLC           | INDIVIDUAL         |
| GT Bank                       | INDIVIDUAL         |
| Keystone Bank PLC             | INDIVIDUAL         |
| Heritage Bank PLC             | INDIVIDUAL         |
| Jaiz Bank                     | INDIVIDUAL         |
| Sterling Bank PLC             | INDIVIDUAL         |
| Suntrust Bank                 | INDIVIDUAL         |
| United Bank For Africa PLC    | INDIVIDUAL         |
| Standard Chartered Bank PLC   | INDIVIDUAL         |
| Polaris Bank PLC              | INDIVIDUAL         |
| Union Bank Of Nigeria PLC     | INDIVIDUAL         |
| Providus Bank                 | INDIVIDUAL         |
| Wema Bank PLC                 | INDIVIDUAL         |
| Zenith International Bank PLC | INDIVIDUAL         |
| Stanbic IBTC Bank PLC         | INDIVIDUAL         |
| Unity Bank PLC                | INDIVIDUAL         |
| CITI Bank                     | BUSINESS           |
| Fidelity Bank PLC             | BUSINESS           |
| First City Monument Bank PLC  | BUSINESS           |
| Access Bank PLC               | BUSINESS           |
| Ecobank Nigeria PLC           | BUSINESS           |
| Afribank Nigeria Plc          | BUSINESS           |
| GT Bank                       | BUSINESS           |
| First Bank Of Nigeria PLC     | BUSINESS           |
| Enterprise Bank Limited       | BUSINESS           |
| Keystone Bank PLC             | BUSINESS           |
| Heritage Bank PLC             | BUSINESS           |
| Jaiz Bank                     | BUSINESS           |
| Providus Bank                 | BUSINESS           |
| Polaris Bank PLC              | BUSINESS           |
| Stanbic IBTC Bank PLC         | BUSINESS           |
| Sterling Bank PLC             | BUSINESS           |
| Unity Bank PLC                | BUSINESS           |
| Suntrust Bank                 | BUSINESS           |
| Union Bank Of Nigeria PLC     | BUSINESS           |
| United Bank For Africa PLC    | BUSINESS           |
| Standard Chartered Bank PLC   | BUSINESS           |
| Wema Bank PLC                 | BUSINESS           |
| Zenith International Bank PLC | BUSINESS           |

