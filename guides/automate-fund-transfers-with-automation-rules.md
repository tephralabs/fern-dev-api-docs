# Automate Fund Transfers with Automation Rules

### Overview

Automation Rules enable you to automate fund movements without manual intervention. Instead of initiating each transaction manually, you can define rules that automatically convert and transfer funds based on triggers you configure.

Automation Rules are particularly useful for:

1. **Scheduled transfers**: Set up recurring transactions (daily, weekly, monthly) to automatically move funds on a schedule. Perfect for regular cash-outs or treasury management.
2. **Threshold-based sweeps**: Automatically trigger transactions when an account balance reaches a specified amount. Ideal for maintaining optimal wallet balances or automated offramping.

### Prerequisites

Before creating automation rules, you must have:

1. **Created a customer**: Use the [Customers API](https://docs.fernhq.com/api-reference/customers) to create either an individual or business customer. The customer does not need to be `ACTIVE` (verified) to set up automation rules.
2. **Created payment accounts**: Set up both source and destination payment accounts via the [Payment Accounts API](https://docs.fernhq.com/api-reference/payment-accounts). You'll need:
   * A **source payment account** (where funds come from) - this must be a Fern crypto wallet
   * A **destination payment account** (where funds go to) - this can be an external bank account, Fern crypto wallet, or external crypto wallet
3. **Payment account IDs**: Have the `paymentAccountId` values ready for both your source and destination accounts. You'll need these when creating the automation rule.

For guidance on creating payment accounts, see:

* [Create Fern wallets](https://docs.fernhq.com/guides/create-fern-wallets) for crypto wallets

### Step-by-step guide

#### Create an automation rule

Create an automation rule using the [Automation Rules API](https://docs.fernhq.com/api-reference/automation-rules). You'll need to specify:

* `name`: A descriptive name for your rule
* `customerId`: The customer who owns this automation rule
* `transactionTemplate`: Configuration for the transactions this rule will create
* `trigger`: Conditions that will execute this rule

**Sample request for a threshold-based rule:**

```json
{
  "name": "Auto sweep USDC to USD when balance reaches $5000",
  "customerId": "4d0b92ee-b48f-471f-8825-61761c0c6eb7",
  "transactionTemplate": {
    "source": {
      "sourcePaymentAccountId": "238e98ce-2305-57e0-83f1-c3286e7db76c",
      "sourcePaymentMethod": "ETHEREUM",
      "sourceCurrency": "USDC"
    },
    "destination": {
      "destinationPaymentAccountId": "8f3a21bc-7609-48d2-9c42-1e5d8a9f2c7e",
      "destinationPaymentMethod": "ACH",
      "destinationCurrency": "USD"
    },
    "amount": {
      "type": "FLAT",
      "value": "5000"
    },
    "developerFee": {
      "developerFeeType": "USD",
      "developerFeeAmount": "5.00"
    }
  },
  "trigger": {
    "type": "THRESHOLD_BASED",
    "thresholdAmount": "5000"
  }
}
```

**Sample request for a time-based rule:**

```json
{
  "name": "Weekly USDC to EUR conversion",
  "customerId": "4d0b92ee-b48f-471f-8825-61761c0c6eb7",
  "transactionTemplate": {
    "source": {
      "sourcePaymentAccountId": "238e98ce-2305-57e0-83f1-c3286e7db76c",
      "sourcePaymentMethod": "ETHEREUM",
      "sourceCurrency": "USDC"
    },
    "destination": {
      "destinationPaymentAccountId": "8f3a21bc-7609-48d2-9c42-1e5d8a9f2c7e",
      "destinationPaymentMethod": "SEPA",
      "destinationCurrency": "EUR"
    },
    "amount": {
      "type": "PCT",
      "value": "100"
    }
  },
  "trigger": {
    "type": "TIME_BASED",
    "cronSchedule": "0 0 * * 1"
  }
}
```

**Sample response:**

```json
{
  "id": "rule_abc123",
  "name": "Auto sweep USDC to USD when balance reaches $5000",
  "customerId": "4d0b92ee-b48f-471f-8825-61761c0c6eb7",
  "transactionTemplate": {
    "source": {
      "sourcePaymentAccountId": "238e98ce-2305-57e0-83f1-c3286e7db76c",
      "sourcePaymentMethod": "ETHEREUM",
      "sourceCurrency": {
        "label": "USDC",
        "chain": "ETHEREUM",
        "contractAddress": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
        "currencyDecimals": 6
      }
    },
    "destination": {
      "destinationPaymentAccountId": "8f3a21bc-7609-48d2-9c42-1e5d8a9f2c7e",
      "destinationPaymentMethod": "ACH",
      "destinationCurrency": {
        "label": "USD"
      }
    },
    "amount": {
      "type": "FLAT",
      "value": "5000"
    }
  },
  "trigger": {
    "type": "THRESHOLD_BASED",
    "thresholdAmount": "5000"
  },
  "createdAt": "2025-10-06T14:23:45.123Z"
}
```

#### List automation rules

Retrieve all automation rules for a customer or filter by source payment account.

**Sample request:**

```bash
GET /automation-rules?customerId=4d0b92ee-b48f-471f-8825-61761c0c6eb7
```

You can also filter by a specific source payment account:

```bash
GET /automation-rules?customerId=4d0b92ee-b48f-471f-8825-61761c0c6eb7&sourcePaymentAccountId=238e98ce-2305-57e0-83f1-c3286e7db76c
```

#### Get a specific automation rule

Retrieve details about a specific automation rule by its ID using the [Automation Rules API](https://docs.fernhq.com/api-reference/automation-rules) `GET` endpoint.

**Sample request:**

```bash
GET /automation-rules/rule_abc123
```

#### Update an automation rule

Modify an existing automation rule to change its configuration. You must provide the complete rule configuration in the update request.

Only the destination parameters, developer fee and amount fields can be updated.

**Sample request:**

```json
{
  "id": "rule_abc123",
  "name": "Updated - increased threshold to $10,000",
  "customerId": "4d0b92ee-b48f-471f-8825-61761c0c6eb7",
  "transactionTemplate": {
    "source": {
      "sourcePaymentAccountId": "238e98ce-2305-57e0-83f1-c3286e7db76c",
      "sourcePaymentMethod": "ETHEREUM",
      "sourceCurrency": "USDC"
    },
    "destination": {
      "destinationPaymentAccountId": "8f3a21bc-7609-48d2-9c42-1e5d8a9f2c7e",
      "destinationPaymentMethod": "ACH",
      "destinationCurrency": "USD"
    },
    "amount": {
      "type": "FLAT",
      "value": "10000"
    }
  },
  "trigger": {
    "type": "THRESHOLD_BASED",
    "thresholdAmount": "10000"
  }
}
```

#### Delete an automation rule

Remove an automation rule when it's no longer needed using the `DELETE` endpoint.

**Sample request:**

```bash
DELETE /automation-rules/rule_abc123
```

### Understanding automation rule components

#### Transaction template

The transaction template defines what transaction will be executed when the rule triggers. It includes:

* **Source**: The payment account and currency funds will come from
* **Destination**: The payment account and currency funds will go to
* **Amount**: How much to transfer (flat amount or percentage of balance)
* **Developer fee** (optional): Additional fees you want to charge

#### Amount types

**FLAT**: Transfer a specific fixed amount

```json
{
  "type": "FLAT",
  "value": "1000"
}
```

**PCT**: Transfer a percentage of the account balance (0-100)

```json
{
  "type": "PCT",
  "value": "50"
}
```

#### Trigger types

**THRESHOLD\_BASED**: Executes when the source account balance reaches or exceeds the specified amount

```json
{
  "type": "THRESHOLD_BASED",
  "thresholdAmount": "5000"
}
```

**TIME\_BASED**: Executes on a schedule defined by a cron expression

```json
{
  "type": "TIME_BASED",
  "cronSchedule": "0 0 * * *"
}
```

**Common cron schedule examples**

* `0 0 * * *` - Daily at midnight UTC
* `0 0 * * 1` - Every Monday at midnight UTC
* `0 12 * * *` - Daily at noon UTC
* `0 0 1 * *` - First day of every month at midnight UTC
* `0 9 * * 1-5` - Weekdays at 9 AM UTC
* `*/15 * * * *` - Every 15 minutes

#### Developer fees

You can optionally charge additional fees on top of Fern's standard fees. The `developerFee` object should be of the following form using the `SOURCE_CURRENCY` type:

**SOURCE\_CURRENCY**: Charge a fee in the source currency

```json
{
  "developerFeeType": "SOURCE_CURRENCY",
  "developerFeeAmount": "10.00"
}
```

### Best practices

1. **Start with clear naming conventions**: Use descriptive names that indicate the purpose, frequency, or trigger condition of each rule. This makes management easier as you scale.
2. **Consider time zones**: Cron schedules use UTC time. Factor this in when setting up time-based rules to match your business operations.
3. **Plan for currency conversion**: Remember that transactions may involve currency conversion with associated fees. Review the exchange rates and fees using the [Exchange Rates API](https://docs.fernhq.com/api-reference/exchange-rates) before setting up rules.
