# First-party offramps

## Overview

Fern's first-party offramps enable your customers to convert any ERC-20 token into a fiat currency, delivering funds to their bank account via local payment rails.

To implement offramps, use the [**Payment Accounts API**](../api-reference/payment-accounts.md), [**Quotes API**](../api-reference/quotes.md), and [**Transactions API**](../api-reference/transactions/). Specify a cryptocurrency as the source and a fiat currency as the destination to configure the transaction as an offramp.



## Step-by-step guide

{% stepper %}
{% step %}
### Create a bank account for a verified customer

Once your customer has been created and successfully verified, you can use the [**Bank Accounts**](../api-reference/payment-accounts.md) endpoint to create a new bank account for the customer.&#x20;

{% hint style="info" %}
Ensure that the beneficiary name matches your customer's name and the account type is accurate. Otherwise, there is a risk the funds will be returned to the sending wallet.&#x20;
{% endhint %}
{% endstep %}

{% step %}
### Generate quote

To fetch a proposed price for a currency conversion, use the [**Quotes**](../api-reference/quotes.md) endpoint. This endpoint generates a quote for your specified currency route, guaranteeing the price for **5 min**. Quotes provide transparent details, including:

* the exact receiving amount,&#x20;
* fees,&#x20;
* minimum guaranteed amount,
* price impact (if applicable).

For more details, visit the [**Quotes**](../api-reference/quotes.md) section.

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://app.fernhq.com/api/v0/quotes/'
--header 'Content-Type: application/json'
--header 'Authorization: Bearer <API_TOKEN>'
--data '{ "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8", "sendingAmount": 100, "source": { "paymentMethod": "base", "currency": "USDC" }, "destination": { "bankAccountId": "072a8b7b-38c7-429a-a6cf-35dae7f2fb77", "paymentMethod": "wire", "currency": "USD" } }'

```
{% endtab %}

{% tab title="Javascript" %}
```javascript
const myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("Authorization", "Bearer <API_TOKEN>");

const raw = JSON.stringify({
  "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
  "sendingAmount": 100,
  "source": {
    "paymentMethod": "ethereum",
    "currency": "USDC"
  },
  "destination": {
    "bankAccountId": "072a8b7b-38c7-429a-a6cf-35dae7f2fb77",
    "paymentMethod": "wire",
    "currency": "USD"
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
```ruby
import http.client
import json

conn = http.client.HTTPConnection("https://app.fernhq.com")
payload = json.dumps({
  "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
  "sendingAmount": 100,
  "source": {
    "paymentMethod": "ethereum",
    "currency": "USDC"
  },
  "destination": {
    "bankAccountId": "072a8b7b-38c7-429a-a6cf-35dae7f2fb77",
    "paymentMethod": "wire",
    "currency": "USD"
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
  "quoteId": "ec823f56-fc7f-493e-8723-9583a91466a7",
  "exchangeRate": 1,
  "receivingAmount": 79.5,
  "minGuaranteedReceivingAmount": 79.5,
  "expiresAt": "2024-12-19T00:29:27.355Z",
  "fees": {
    "fernFee": {
      "type": "fern",
      "currency": "USD",
      "amount": 20.5,
      "usdAmount": 20.5
    },
    "developerFee": {
      "type": "developer",
      "currency": "USD",
      "amount": 0,
      "usdAmount": 0,
    },
  }
}
```


{% endstep %}

{% step %}
### Submit transaction

To create a transaction, use the **quote ID** to generate an offramp transaction. Once the transaction is initiated and funds are received at the transfer wallet address, the destination currency is sent to the specified destination address via the indicated payment rail.&#x20;

For more details, check out the [**Transactions**](../api-reference/transactions/) section.

#### Request

{% tabs %}
{% tab title="cURL" %}
```javascript
curl --location 'https://app.fernhq.com/api/v0/transactions/' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <API_TOKEN>' \
--data '{
    "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
    "quoteId": "ec823f56-fc7f-493e-8723-9583a91466a7"
}'
```
{% endtab %}

{% tab title="Javascript" %}
```python
const myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("Authorization", "Bearer fsk_ZGM5MDNmNDgtNjQzZi00NDdlLWEwNDktNjcwMDUzZGIyNWFkXzQxM2UwZTRhOWRiZjI2YjhhYzUyMjBkMzBmZWMzMzhmN2ZmOTE1NzYxMDEzYWZiNjA3OGQxNzNlNjA1YmUyMzI");

const raw = JSON.stringify({
  "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
  "quoteId": "ec823f56-fc7f-493e-8723-9583a91466a7"
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
```ruby
import http.client
import json

conn = http.client.HTTPSConnection("app.fernhq.com")
payload = json.dumps({
  "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
  "quoteId": "ec823f56-fc7f-493e-8723-9583a91466a7"
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
  "transactionId": "1d8beb26-b4d1-47ee-8e5d-0d3905f200c7",
  "quoteId": "ec823f56-fc7f-493e-8723-9583a91466a7",
  "status": "PROCESSING",
  "receivingAmountDetails": {
    "amount" : 79.5,
    "currency": "USD",
  },
  "fees": {
    "fernFee": {
      "type": "fern",
      "currency": "USD",
      "amount": 20.5,
      "usdAmount": 20.5
    },
    "developerFee": {
      "type": "developer",
      "currency": "USD",
      "amount": 0,
      "usdAmount": 0,
    },
  }
  "exchangeRate": 1,
  "transferInstructions": {
    "type": "offramp",
    "paymentMethod": "ethereum",
    "transferWalletAddress": "0xbf50fb9c99233f95bce213044507e58485cf9eba",
    "sendingAmountDetails": {
      "amount": 100,
      "currency": "USDC",
    }
  }
}
```


{% endstep %}

{% step %}
### Monitor transaction status

Track the progress of a transaction by calling the [**Transactions**](../api-reference/transactions/#api-v0-transactions-transactionid) endpoint or visiting the **Developer dashboard**. You can also subscribe to webhooks for real-time notifications on status changes (coming soon).

For a full list of transaction statuses, refer to [**Transaction Statuses**](../api-reference/transactions/introduction.md#transaction-statuses).

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://app.fernhq.com/api/v0/transactions/1d8beb26-b4d1-47ee-8e5d-0d3905f200c7' \
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

fetch("https://app.fernhq.com/api/v0/transactions/1d8beb26-b4d1-47ee-8e5d-0d3905f200c7", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```
{% endtab %}

{% tab title="Python" %}
```python
import http.client

conn = http.client.HTTPConnection("app.fernhq.com")
payload = ''
headers = {
  'Authorization': 'Bearer <API_TOKEN>'
}
conn.request("GET", "/api/v0/transactions/1d8beb26-b4d1-47ee-8e5d-0d3905f200c7", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))
```
{% endtab %}
{% endtabs %}

#### Response

```json
{
  "transactionId": "1d8beb26-b4d1-47ee-8e5d-0d3905f200c7",
  "quoteId": "ec823f56-fc7f-493e-8723-9583a91466a7",
  "status": "PROCESSING",
  "receivingAmountDetails": {
    "amount" : 79.5,
    "currency": "USD",
  },
  "fees": {
    "fernFee": {
      "type": "fern",
      "currency": "USD",
      "amount": 20.5,
      "usdAmount": 20.5
    },
    "developerFee": {
      "type": "developer",
      "currency": "USD",
      "amount": 0,
      "usdAmount": 0,
    },
  }
  "exchangeRate": 1,
  "transferInstructions": {
    "type": "offramp",
    "paymentMethod": "ethereum",
    "transferWalletAddress": "0xbf50fb9c99233f95bce213044507e58485cf9eba",
    "sendingAmountDetails": {
      "amount": 100,
      "currency": "USDC",
    }
  }
}
```


{% endstep %}
{% endstepper %}

