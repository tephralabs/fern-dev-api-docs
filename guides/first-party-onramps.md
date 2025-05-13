# First-party onramps

## Overview

Fern's first-party onramps enable your customers to convert fiat into crypto, delivering funds directly to their wallets. Fern onramps are accessible via API, allowing you to design a seamless and customized user experience.

To implement onramps, use the [**Payment Accounts API**](../api-reference/payment-accounts.md), [**Quotes API**](../api-reference/quotes.md), and [**Transactions API**](../api-reference/transactions/). Specify a fiat currency as the source and a cryptocurrency as the destination to configure the transaction as an onramp.

## Step-by-step guide

{% stepper %}
{% step %}
### Create a bank account for a verified customer

Once your customer has been created and successfully verified, you can use the [**Bank Accounts**](../api-reference/payment-accounts.md) endpoint to create a new bank account for the customer.&#x20;

{% hint style="info" %}
Ensure that the beneficiary name matches your customer's name and the account type is accurate. Otherwise, there is a risk the funds will be returned to the sending bank account.&#x20;
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
curl --location 'https://api.fernhq.com/quotes/' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <API_TOKEN>' \
--data '{
    "customerId": "b8643416-7a36-4437-95af-c32ba3b44257",
    "source": {
        "sourcePaymentAccountId": "550f6386-9ae0-4aa8-87f1-74f628e32836",
        "sourceCurrency": "USD",
        "sourcePaymentMethod": "ACH",
        "sourceAmount": "11"
    },
    "destination": {
        "destinationPaymentAccountId": "9e67ed18-4b77-41e6-8839-5d00e0b4df22",
        "destinationPaymentMethod": "BASE",
        "destinationCurrency": "USDC"
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
    "customerId": "b8643416-7a36-4437-95af-c32ba3b44257",
    "source": {
        "sourcePaymentAccountId": "550f6386-9ae0-4aa8-87f1-74f628e32836",
        "sourceCurrency": "USD",
        "sourcePaymentMethod": "ACH",
        "sourceAmount": "11"
    },
    "destination": {
        "destinationPaymentAccountId": "9e67ed18-4b77-41e6-8839-5d00e0b4df22",
        "destinationPaymentMethod": "BASE",
        "destinationCurrency": "USDC"
    }
});

const requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow"
};

fetch("https://api.fernhq.com/quotes/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```
{% endtab %}

{% tab title="Python" %}
```python
import http.client
import json

conn = http.client.HTTPSConnection("api.fernhq.com")
payload = json.dumps({
    "customerId": "b8643416-7a36-4437-95af-c32ba3b44257",
    "source": {
        "sourcePaymentAccountId": "550f6386-9ae0-4aa8-87f1-74f628e32836",
        "sourceCurrency": "USD",
        "sourcePaymentMethod": "ACH",
        "sourceAmount": "11"
    },
    "destination": {
        "destinationPaymentAccountId": "9e67ed18-4b77-41e6-8839-5d00e0b4df22",
        "destinationPaymentMethod": "BASE",
        "destinationCurrency": "USDC"
    }
})
headers = {
  'Content-Type': 'application/json',
  'Authorization': 'Bearer <API_TOKEN>'
}
conn.request("POST", "/quotes/", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))
```
{% endtab %}
{% endtabs %}

#### Response

```json
{
    "quoteId": "672cec1f-8224-41ca-b77a-46307ae0f213",
    "expiresAt": "2025-04-08T20:36:09.129Z",
    "estimatedExchangeRate": "1",
    "destinationAmount": "10.4789850245923121",
    "minGuaranteedDestinationAmount": "10.4789850245923121",
    "fees": {
        "feeCurrency": {
            "label": "USD"
        },
        "fernFee": {
            "feeAmount": "0.522",
            "feeUSDAmount": "0.522"
        },
        "developerFee": {
            "feeAmount": "0",
            "feeUSDAmount": "0"
        }
    }
}
```


{% endstep %}

{% step %}
### Submit transaction

To create a transaction, use the **quote ID** to generate an onramp transaction. Once the transaction is created, you'll receive transfer instructions to share with your customer. Once funds are received at the transfer bank account, the destination currency is sent to the specified destination address, completing the transaction within seconds or minutes.

For more details, check out the [**Transactions**](../api-reference/transactions/) section.

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://api.fernhq.com/transactions/' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <API_TOKEN>' \
--data '{
    "quoteId": "672cec1f-8224-41ca-b77a-46307ae0f213"
}'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("Authorization", "Bearer <API_TOKEN>");

const raw = JSON.stringify({
  "quoteId": "672cec1f-8224-41ca-b77a-46307ae0f213"
});

const requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow"
};

fetch("https://api.fernhq.com/transactions/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```
{% endtab %}

{% tab title="Python" %}
```python
import http.client
import json

conn = http.client.HTTPSConnection("api.fernhq.com")
payload = json.dumps({
  "quoteId": "672cec1f-8224-41ca-b77a-46307ae0f213"
})
headers = {
  'Content-Type': 'application/json',
  'Authorization': 'Bearer <API_TOKEN>'
}
conn.request("POST", "/transactions/", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))
```
{% endtab %}
{% endtabs %}

#### Response

```json
{
    "transactionId": "477f21e2-1b67-5828-a43d-dab19316a711",
    "transactionStatus": "AWAITING_TRANSFER",
    "source": {
        "sourceCurrency": {
            "label": "USD"
        },
        "sourcePaymentMethod": "ACH",
        "sourceAmount": "11",
        "sourcePaymentAccountId": "550f6386-9ae0-4aa8-87f1-74f628e32836",
    },
    "destination": {
        "destinationPaymentAccountId": "9e67ed18-4b77-41e6-8839-5d00e0b4df22",
        "destinationPaymentMethod": "BASE",
        "destinationCurrency": {
            "label": "USDC"
        },
        "exchangeRate": "1",
        "destinationAmount": "10.47",
        "minGuaranteedDestinationAmount": "10.4789850245923121"
    },
    "fees": {
        "feeCurrency": {
            "label": "USD"
        },
        "fernFee": {
            "feeAmount": "0.522",
            "feeUSDAmount": "0.522"
        },
        "developerFee": {
            "feeAmount": "0",
            "feeUSDAmount": "0"
        }
    },
    "createdAt": "2025-04-08T20:30:09.129Z",
    "correlationId": "",
    "transferInstructions": {
        "type": "fiat",
        "transferPaymentMethod": "ACH",
        "transferMessage": "ABCXXXXXXXXXXXX",
        "transferBankName": "Test Bank",
        "transferBankAddress": "500 Main St., Some City, SC 99999",
        "transferBankAccountNumber": "123456123456",
        "transferBankBeneficiaryName": "Bank Name",
        "transferACHRoutingNumber": "123456789"
    }
}
```


{% endstep %}

{% step %}
### Monitor transaction status

Track the progress of a transaction by calling the [**Transactions**](../api-reference/transactions/#api-v0-transactions-transactionid) endpoint or visiting the [**Developer dashboard**](../overview/developer-dashboard.md). You can also subscribe to webhooks for real-time notifications on status changes (coming soon).

For a full list of transaction statuses, refer to [**Transaction Statuses**](../api-reference/transactions/introduction.md#transaction-statuses).

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://api.fernhq.com/transactions/477f21e2-1b67-5828-a43d-dab19316a711' \
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

fetch("https://api.fernhq.com/transactions/477f21e2-1b67-5828-a43d-dab19316a711", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```
{% endtab %}

{% tab title="Python" %}
```python
import http.client

conn = http.client.HTTPSConnection("api.fernhq.com")
payload = ''
headers = {
  'Authorization': 'Bearer <API_TOKEN>'
}
conn.request("GET", "/transactions/477f21e2-1b67-5828-a43d-dab19316a711", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))
```
{% endtab %}
{% endtabs %}

#### Response

```json
{
    "transactionId": "477f21e2-1b67-5828-a43d-dab19316a711",
    "transactionStatus": "AWAITING_TRANSFER",
    "source": {
        "sourceCurrency": {
            "label": "USD"
        },
        "sourcePaymentMethod": "ACH",
        "sourceAmount": "11",
        "sourcePaymentAccountId": "550f6386-9ae0-4aa8-87f1-74f628e32836",
    },
    "destination": {
        "destinationPaymentAccountId": "9e67ed18-4b77-41e6-8839-5d00e0b4df22",
        "destinationPaymentMethod": "BASE",
        "destinationCurrency": {
            "label": "USDC"
        },
        "exchangeRate": "1",
        "destinationAmount": "10.47",
        "minGuaranteedDestinationAmount": "10.4789850245923121"
    },
    "fees": {
        "feeCurrency": {
            "label": "USD"
        },
        "fernFee": {
            "feeAmount": "0.522",
            "feeUSDAmount": "0.522"
        },
        "developerFee": {
            "feeAmount": "0",
            "feeUSDAmount": "0"
        }
    },
    "createdAt": "2025-04-08T20:30:09.129Z",
    "correlationId": "",
    "transferInstructions": {
        "type": "fiat",
        "transferPaymentMethod": "ACH",
        "transferMessage": "ABCXXXXXXXXXXXX",
        "transferBankName": "Test Bank",
        "transferBankAddress": "500 Main St., Some City, SC 99999",
        "transferBankAccountNumber": "123456123456",
        "transferBankBeneficiaryName": "Bank Name",
        "transferACHRoutingNumber": "123456789"
    }
}
```


{% endstep %}
{% endstepper %}

