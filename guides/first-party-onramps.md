# First-party onramps

### Overview

Fern's first-party onramps enable your customers to convert fiat into crypto, delivering funds directly to their wallets—whether external (e.g. Metamask) or an embedded wallet (e.g. Privy). The onramps are accessible via API, allowing you to design a seamless and customized user experience.

To implement onramps, use the **Quotes API** and [**Transactions API**](../api-reference/transactions/). Specify a fiat currency as the source and a cryptocurrency as the destination to configure the transaction as an onramp.

For the latest list of supported fiat and cryptocurrencies, as well as payment methods, refer to the [**Supported Coverage**](../overview/supported-regions-and-currencies.md) section.

### Step-by-step guide

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

To fetch a proposed price for a currency conversion, use the **Quotes** endpoint. This endpoint generates a quote for your specified currency route, guaranteeing the price for **5 mins**. Quotes provide transparent details, including the exact receiving amount and any applicable fees.

For more details, check out the **Quotes** section.

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://fern-git-feat-devprod-tephra-labs.vercel.app/api/v0/quotes/' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <API_TOKEN>' \
--data '{
    "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
    "sendingAmount": 10,
    "source": {
        "bankAccountId": "a79f75i1-d2a5-49ca-8bb4-5edf0b965cd8user5bank0",
        "paymentRail": "wire",
        "currency": "USD"
    },
    "destination": {
        "walletAddress": "0x4b6f5a3dfc911e992c3d8f38c6bb9d1563b5e9a5",
        "paymentRail": "sepolia",
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
  "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
  "sendingAmount": 10,
  "source": {
    "bankAccountId": "a79f75i1-d2a5-49ca-8bb4-5edf0b965cd8user5bank0",
    "paymentRail": "wire",
    "currency": "USD"
  },
  "destination": {
    "walletAddress": "0x4b6f5a3dfc911e992c3d8f38c6bb9d1563b5e9a5",
    "paymentRail": "sepolia",
    "currency": "USDC"
  }
});

const requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow"
};

fetch("https://fern-git-feat-devprod-tephra-labs.vercel.app/api/v0/quotes/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```
{% endtab %}

{% tab title="Python" %}
```python
import http.client
import json

conn = http.client.HTTPSConnection("fern-git-feat-devprod-tephra-labs.vercel.app")
payload = json.dumps({
  "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
  "sendingAmount": 10,
  "source": {
    "bankAccountId": "a79f75i1-d2a5-49ca-8bb4-5edf0b965cd8user5bank0",
    "paymentRail": "wire",
    "currency": "USD"
  },
  "destination": {
    "walletAddress": "0x4b6f5a3dfc911e992c3d8f38c6bb9d1563b5e9a5",
    "paymentRail": "sepolia",
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

````json
```json
{
    "quoteId": "c88ebfc2-3356-48b5-b2bb-6fca7b618c96",
    "exchangeRate": 1,
    "receivingAmount": 9.925,
    "minGuaranteedReceivingAmount": 9.925,
    "expiresAt": "2024-12-19T01:04:54.783Z",
    "fees": {
        "fernFee": {
            "type": "fern",
            "currency": "USDC",
            "amount": 0.075
        },
        "fiatFee": {
            "type": "fiat",
            "currency": "USDC",
            "amount": 0
        },
        "gasFee": {
            "type": "gas",
            "currency": "USDC",
            "amount": 0
        }
    }
}
```
````


{% endstep %}

{% step %}
### Submit transaction

To create a transaction, use the **quoteID** to generate an order for fulfillment. Once the transaction is initiated and funds are received at the target address, the destination currency is sent to the specified destination address, completing the transaction within seconds or minutes.

For more details, check out the [**Transactions**](../api-reference/transactions/) section.

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://fern-git-feat-devprod-tephra-labs.vercel.app/api/v0/transactions/' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <API_TOKEN>' \
--data '{
    "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
    "quoteId": "c88ebfc2-3356-48b5-b2bb-6fca7b618c96"
}'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("Authorization", "Bearer <API_TOKEN>");

const raw = JSON.stringify({
  "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
  "quoteId": "c88ebfc2-3356-48b5-b2bb-6fca7b618c96"
});

const requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow"
};

fetch("https://fern-git-feat-devprod-tephra-labs.vercel.app/api/v0/transactions/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```
{% endtab %}

{% tab title="Python" %}
```python
import http.client
import json

conn = http.client.HTTPSConnection("fern-git-feat-devprod-tephra-labs.vercel.app")
payload = json.dumps({
  "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
  "quoteId": "c88ebfc2-3356-48b5-b2bb-6fca7b618c96"
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

````json
```json
{
  "transactionId": "908c4092-3a5e-44c8-ac4e-ad47993f00e9",
  "status": "AWAITING_TRANSFER",
  "receivingAmount": 99.25,
  "fees": {
    "fernFee": {
      "type": "fern",
      "currency": "USD",
      "amount": 0.75
    },
    "gasFee": {
      "type": "gas",
      "currency": "USD",
      "amount": 0
    }
  },
  "exchangeRate": 1,
  "transferInstructions": {
    "type": "onramp",
    "paymentRail": "wire",
    "transferMessage": "test onramp",
    "transferBankName": "Chase",
    "transferBankAddress": "10 Downing Steet",
    "transferBankRoutingNumber": "1234",
    "transferBankAccountNumber": "10029000",
    "transferBankBeneficiaryName": "Fern Money"
  }
}
```
````


{% endstep %}

{% step %}
### Track transaction status

Track the progress of a transaction by calling the **Transactions endpoint** or visiting the **Developer Dashboard**. You can also subscribe to webhooks for real-time notifications on status changes (coming soon).

For a full list of transaction statuses, refer to the **Transaction Statuses Documentation**.

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://fern-git-feat-devprod-tephra-labs.vercel.app/api/v0/transactions/' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <API_TOKEN>' \
--data '{
    "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
    "quoteId": "c88ebfc2-3356-48b5-b2bb-6fca7b618c96"
}'
```


{% endtab %}

{% tab title="JavaScript" %}
```javascript
const myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("Authorization", "Bearer <API_TOKEN>");

const raw = JSON.stringify({
  "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
  "quoteId": "c88ebfc2-3356-48b5-b2bb-6fca7b618c96"
});

const requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow"
};

fetch("https://fern-git-feat-devprod-tephra-labs.vercel.app/api/v0/transactions/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```
{% endtab %}

{% tab title="Python" %}
```python
import http.client
import json

conn = http.client.HTTPSConnection("fern-git-feat-devprod-tephra-labs.vercel.app")
payload = json.dumps({
  "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
  "quoteId": "c88ebfc2-3356-48b5-b2bb-6fca7b618c96"
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

````json
```json
{
  "transactionId": "908c4092-3a5e-44c8-ac4e-ad47993f00e9",
  "status": "AWAITING_TRANSFER",
  "receivingAmount": 99.25,
  "fees": {
    "fernFee": {
      "type": "fern",
      "currency": "USD",
      "amount": 0.75
    },
    "gasFee": {
      "type": "gas",
      "currency": "USD",
      "amount": 0
    }
  },
  "exchangeRate": 1,
  "transferInstructions": {
    "type": "onramp",
    "paymentRail": "wire",
    "transferMessage": "test onramp",
    "transferBankName": "Chase",
    "transferBankAddress": "10 Downing Steet",
    "transferBankRoutingNumber": "1234",
    "transferBankAccountNumber": "10029000",
    "transferBankBeneficiaryName": "Fern Money"
  }
}
```
````


{% endstep %}
{% endstepper %}

