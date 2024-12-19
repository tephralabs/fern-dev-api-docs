# First-party onramps

## Overview

Fern's first-party onramps enable your customers to convert fiat into crypto, delivering funds directly to their wallets—whether external (e.g. Metamask) or an embedded wallet (e.g. Privy). The onramps are accessible via API, allowing you to design a seamless and customized user experience.

To implement onramps, use the [**Quotes API**](../api-reference/quotes.md) and [**Transactions API**](../api-reference/transactions/). Specify a fiat currency as the source and a cryptocurrency as the destination to configure the transaction as an onramp.

For the latest list of supported fiat and cryptocurrencies, as well as payment methods, refer to the [**Supported Coverage**](../overview/supported-regions-and-currencies.md) section.

## Step-by-step guide

{% stepper %}
{% step %}
### Add a bank account to an existing customer

Use the [**Bank Accounts**](../group-1/bank-accounts.md) endpoint to create a new bank account.&#x20;

{% hint style="info" %}
Ensure that the beneficiary name and account type matches the customer details. Otherwise, the funds will be returned to the sending bank account.&#x20;
{% endhint %}
{% endstep %}

{% step %}
### Generate quote

To fetch a proposed price for a currency conversion, use the [**Quotes**](../api-reference/quotes.md) endpoint. This endpoint generates a quote for your specified currency route, guaranteeing the price for **5 mins**. Quotes provide transparent details, including the exact receiving amount and any applicable fees.

For more details, check out the [**Quotes**](../api-reference/quotes.md) section.

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://app.fernhq.com/api/v0/quotes/' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <API_TOKEN>' \
--data '{
    "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
    "sendingAmount": 100,
    "source": {
        "bankAccountId": "072a8b7b-38c7-429a-a6cf-35dae7f2fb77",
        "paymentRail": "wire",
        "currency": "USD"
    },
    "destination": {
        "walletAddress": "0xbf50fb9c99233f95bce213044507e58485cf9eba",
        "paymentRail": "ethereum",
        "currency": "USDC"
    }
}'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("Authorization", "Bearer <API_TOKEN>");

const raw = JSON.stringify({
  "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
  "sendingAmount": 10,
  "source": {
    "bankAccountId": "072a8b7b-38c7-429a-a6cf-35dae7f2fb77",
    "paymentRail": "wire",
    "currency": "USD"
  },
  "destination": {
    "walletAddress": "0xbf50fb9c99233f95bce213044507e58485cf9eba",
    "paymentRail": "ethereum",
    "currency": "USDC"
  }
});

const requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow"
};

fetch("https://app.fernhq.com/api/v0/quotes/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```
{% endtab %}

{% tab title="Python" %}
```python
import http.client
import json

conn = http.client.HTTPSConnection("app.fernhq.com")
payload = json.dumps({
  "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
  "sendingAmount": 10,
  "source": {
    "bankAccountId": "072a8b7b-38c7-429a-a6cf-35dae7f2fb77",
    "paymentRail": "wire",
    "currency": "USD"
  },
  "destination": {
    "walletAddress": "0xbf50fb9c99233f95bce213044507e58485cf9eba",
    "paymentRail": "ethereum",
    "currency": "USDC"
  }
})
headers = {
  'Content-Type': 'application/json',
  'Authorization': 'Bearer <API_TOKEN>'
}
conn.request("POST", "/api/v0/quotes/", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))
```
{% endtab %}
{% endtabs %}

#### Response

```json
{
  "quoteId": "072a8b7b-3356-48b5-b2bb-6fca7b618c96",
  "exchangeRate": 1,
  "receivingAmount": 99.25,
  "minGuaranteedReceivingAmount": 99.25,
  "expiresAt": "2024-12-19T01:04:54.783Z",
  "fees": {
    "fernFee": {
      "type": "fern",
      "currency": "USDC",
      "amount": 0.75,
      "usdAmount": 0.75
    },
    "developerFee": {
      "type": "developer",
      "currency": "USDC",
      "amount": 0,
      "usdAmount": 0
    }
  }
}
```


{% endstep %}

{% step %}
### Submit transaction

To create a transaction, use the **quoteID** to generate an order for fulfillment. Once the transaction is initiated and funds are received at the target address, the destination currency is sent to the specified destination address, completing the transaction within seconds or minutes.

For more details, check out the [**Transactions**](../api-reference/transactions/) section.

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://app.fernhq.com/api/v0/transactions/' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <API_TOKEN>' \
--data '{
    "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
    "quoteId": "072a8b7b-3356-48b5-b2bb-6fca7b618c96"
}'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("Authorization", "Bearer <API_TOKEN>");

const raw = JSON.stringify({
  "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
  "quoteId": "072a8b7b-3356-48b5-b2bb-6fca7b618c96"
});

const requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow"
};

fetch("https://app.fernhq.com/api/v0/transactions/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```
{% endtab %}

{% tab title="Python" %}
```python
import http.client
import json

conn = http.client.HTTPSConnection("app.fernhq.com")
payload = json.dumps({
  "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
  "quoteId": "072a8b7b-3356-48b5-b2bb-6fca7b618c96"
})
headers = {
  'Content-Type': 'application/json',
  'Authorization': 'Bearer <API_TOKEN>'
}
conn.request("POST", "/api/v0/transactions/", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))
```
{% endtab %}
{% endtabs %}

#### Response

```json
{
  "quoteId": "072a8b7b-3356-48b5-b2bb-6fca7b618c96",
  "transactionId": "908c4092-3a5e-44c8-ac4e-ad47993f00e9",
  "status": "AWAITING_TRANSFER",
  "receivingAmountDetails": {
    "amount": 99.25,
    "currency": "USDC",
  },
  "fees": {
    "fernFee": {
      "type": "fern",
      "currency": "USDC",
      "amount": 0.75,
      "usdAmount": 0.75
    },
    "developerFee": {
      "type": "developer",
      "currency": "USDC",
      "amount": 0,
      "usdAmount": 0
    }
  },
  "exchangeRate": 1,
  "transferInstructions": {
    "type": "onramp",
    "paymentRail": "wire",
    "transferMessage": "Test onramp wire",
    "transferBankName": "Chase",
    "transferBankAddress": "10 Downing Steet",
    "transferBankRoutingNumber": "1234",
    "transferBankAccountNumber": "10029000",
    "transferBankBeneficiaryName": "Fern Money"
  }
}
```


{% endstep %}

{% step %}
### Track transaction status

Track the progress of a transaction by calling the [**Transactions**](../api-reference/transactions/#api-v0-transactions-transactionid) endpoint or visiting the **Developer Dashboard**. You can also subscribe to webhooks for real-time notifications on status changes (coming soon).

For a full list of transaction statuses, refer to [**Transaction Statuses**](../api-reference/transactions/introduction.md#transaction-statuses).

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://app.fernhq.com/api/v0/transactions/908c4092-3a5e-44c8-ac4e-ad47993f00e9' \
--header 'Authorization: Bearer <API_TOKEN>'
```


{% endtab %}

{% tab title="JavaScript" %}
```javascript
const myHeaders = new Headers();
myHeaders.append("Authorization", "Bearer <API_TOKEN>");

const requestOptions = {
  method: "GET",
  headers: myHeaders,
  redirect: "follow"
};

fetch("https://app.fernhq.com/api/v0/transactions/908c4092-3a5e-44c8-ac4e-ad47993f00e9", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```
{% endtab %}

{% tab title="Python" %}
```python
import http.client

conn = http.client.HTTPSConnection("app.fernhq.com")
payload = ''
headers = {
  'Authorization': 'Bearer <API_TOKEN>'
}
conn.request("GET", "/api/v0/transactions/908c4092-3a5e-44c8-ac4e-ad47993f00e9", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))
```
{% endtab %}
{% endtabs %}

#### Response

```json
{
  "quoteId": "072a8b7b-3356-48b5-b2bb-6fca7b618c96",
  "transactionId": "908c4092-3a5e-44c8-ac4e-ad47993f00e9",
  "status": "AWAITING_TRANSFER",
  "receivingAmountDetails": {
    "amount": 99.25,
    "currency": "USDC",
  },
  "fees": {
    "fernFee": {
      "type": "fern",
      "currency": "USDC",
      "amount": 0.75,
      "usdAmount": 0.75
    },
    "developerFee": {
      "type": "developer",
      "currency": "USDC",
      "amount": 0,
      "usdAmount": 0
    }
  },
  "exchangeRate": 1,
  "transferInstructions": {
    "type": "onramp",
    "paymentRail": "wire",
    "transferMessage": "Test onramp wire",
    "transferBankName": "Chase",
    "transferBankAddress": "10 Downing Steet",
    "transferBankRoutingNumber": "1234",
    "transferBankAccountNumber": "10029000",
    "transferBankBeneficiaryName": "Fern Money"
  }
}
```


{% endstep %}
{% endstepper %}

