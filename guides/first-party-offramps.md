# First-party offramps

### Overview

Fern's first-party offramps enable your customers to convert any ERC-20 into fiat, delivering funds to their bank account via local payment rails. &#x20;

To implement offramps, use the **Quotes API** and [**Transactions API**](../api-reference/transactions/). Specify a cryptocurrency as the source and a fiat currency as the destination to configure the transaction as an offramp.

For the latest list of supported fiat and cryptocurrencies, as well as payment methods, refer to the [**Supported Coverage**](../overview/supported-regions-and-currencies.md) section.

### Step-by-step guide

{% stepper %}
{% step %}
### Add a bank account to an existing customer

Use the [**Bank Accounts**](../group-1/bank-accounts.md) endpoint to create a new bank account.&#x20;

{% hint style="info" %}
Ensure that the beneficiary name and account type matches the customer's details. Otherwise, the transaction will fail and funds will not be transferred.&#x20;
{% endhint %}
{% endstep %}

{% step %}
### Generate quote

To fetch a proposed price for a currency conversion, use the **Quotes** endpoint. This endpoint generates a quote for your specified currency route, guaranteeing the price for **5 min**. Quotes provide transparent details, including:

* the exact receiving amount,&#x20;
* fees,&#x20;
* minimum guaranteed amount,
* price impact (if applicable).

For more details, visit the **Quotes** section.

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://fern-git-feat-devprod-tephra-labs.vercel.app/api/v0/quotes/'
--header 'Content-Type: application/json'
--header 'Authorization: Bearer <API_TOKEN>'
--data '{ "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5", "sendingAmount": 100, "source": { "walletAddress": "0x4b6f5a3dfc911e992c3d8f38c6bb9d1563b5e9a5", "paymentRail": "base", "currency": "USDC" }, "destination": { "bankAccountId": "a79f75i1-d2a5-49ca-8bb4-5edf0b965cd8user5bank0", "paymentRail": "wire", "currency": "USD" } }'
```
{% endtab %}

{% tab title="Javascript" %}
```javascript
const myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("Authorization", "Bearer <API_TOKEN>");

const raw = JSON.stringify({
  "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
  "sendingAmount": 100,
  "source": {
    "walletAddress": "0x4b6f5a3dfc911e992c3d8f38c6bb9d1563b5e9a5",
    "paymentRail": "sepolia",
    "currency": "USDC"
  },
  "destination": {
    "bankAccountId": "a79f75i1-d2a5-49ca-8bb4-5edf0b965cd8user5bank0",
    "paymentRail": "wire",
    "currency": "USD"
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
```ruby
import http.client
import json

conn = http.client.HTTPConnection("https://fern-git-feat-devprod-tephra-labs.vercel.app")
payload = json.dumps({
  "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
  "sendingAmount": 100,
  "source": {
    "walletAddress": "0x4b6f5a3dfc911e992c3d8f38c6bb9d1563b5e9a5",
    "paymentRail": "sepolia",
    "currency": "USDC"
  },
  "destination": {
    "bankAccountId": "a79f75i1-d2a5-49ca-8bb4-5edf0b965cd8user5bank0",
    "paymentRail": "wire",
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
            "amount": 0.5
        },
        "fiatFee": {
            "type": "fiat",
            "currency": "USD",
            "amount": 20
        },
        "gasFee": {
            "type": "gas",
            "currency": "USD",
            "amount": 0
        }
    }
}
```


{% endstep %}

{% step %}
### Submit transaction

To create a transaction, use the **quoteID** to generate an order for fulfillment. Once the transaction is initiated and funds are received at the target address, the destination currency is sent to the specified destination address via the indicated payment rail.&#x20;

For more details, check out the [**Transactions**](../api-reference/transactions/) section.

#### Request

{% tabs %}
{% tab title="cURL" %}
```javascript
curl --location 'https://fern-git-feat-devprod-tephra-labs.vercel.app/api/v0/transactions/' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer fsk_ZGM5MDNmNDgtNjQzZi00NDdlLWEwNDktNjcwMDUzZGIyNWFkXzQxM2UwZTRhOWRiZjI2YjhhYzUyMjBkMzBmZWMzMzhmN2ZmOTE1NzYxMDEzYWZiNjA3OGQxNzNlNjA1YmUyMzI' \
--data '{
    "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
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
  "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
  "quoteId": "ec823f56-fc7f-493e-8723-9583a91466a7"
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
```ruby
import http.client
import json

conn = http.client.HTTPSConnection("fern-git-feat-devprod-tephra-labs.vercel.app")
payload = json.dumps({
  "customerId": "c2b7c8d0-5c3a-11ec-8d3d-0242ac1account5",
  "quoteId": "ec823f56-fc7f-493e-8723-9583a91466a7"
})
headers = {
  'Content-Type': 'application/json',
  'Authorization': 'Bearer fsk_ZGM5MDNmNDgtNjQzZi00NDdlLWEwNDktNjcwMDUzZGIyNWFkXzQxM2UwZTRhOWRiZjI2YjhhYzUyMjBkMzBmZWMzMzhmN2ZmOTE1NzYxMDEzYWZiNjA3OGQxNzNlNjA1YmUyMzI'
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
    "id": "1d8beb26-b4d1-47ee-8e5d-0d3905f200c7",
    "status": "PROCESSING",
    "receivingAmount": 79.5,
    "fees": {
        "fernFee": {
            "type": "fern",
            "currency": "USD",
            "amount": 0.5
        },
        "gasFee": {
            "type": "gas",
            "currency": "USD",
            "amount": 0
        }
    },
    "exchangeRate": 1,
    "transferInstructions": {
        "type": "offramp",
        "paymentRail": "sepolia",
        "transferWalletAddress": "0x4b6f5a3dfc911e992c3d8f38c6bb9d1563b5e9a5"
    }
}
```


{% endstep %}

{% step %}
### Track transaction status

Track the progress of a transaction by calling the **XXX endpoint** or visiting the **Developer Dashboard**. You can also subscribe to webhooks for real-time notifications on status changes (coming soon).

For a full list of transaction statuses, refer to the **Transaction Statuses Documentation**.

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://fern-git-feat-devprod-tephra-labs.vercel.app/api/v0/transactions/1d8beb26-b4d1-47ee-8e5d-0d3905f200c7' \
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

fetch("https://fern-git-feat-devprod-tephra-labs.vercel.app/api/v0/transactions/1d8beb26-b4d1-47ee-8e5d-0d3905f200c7", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```
{% endtab %}

{% tab title="Python" %}
```python
import http.client

conn = http.client.HTTPConnection("fern-git-feat-devprod-tephra-labs.vercel.app")
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
    "status": "PROCESSING",
    "receivingAmount": 79.5,
    "fees": {
        "fernFee": {
            "type": "fern",
            "currency": "USD",
            "amount": 0.5
        },
        "gasFee": {
            "type": "gas",
            "currency": "USD",
            "amount": 0
        }
    },
    "exchangeRate": 1,
    "transferInstructions": {
        "type": "offramp",
        "paymentRail": "sepolia",
        "transferWalletAddress": "0x4b6f5a3dfc911e992c3d8f38c6bb9d1563b5e9a5"
    }
}
```


{% endstep %}
{% endstepper %}

