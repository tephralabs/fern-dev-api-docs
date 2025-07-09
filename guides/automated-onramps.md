# Automated onramps

**For individuals, Fern offers named accounts that can collect and automatically onramp the following fiat currencies**

* USD
* EUR
* MXN

## Step-by-step guide

{% stepper %}
{% step %}
### Create a payment account for a verified customer

Once your customer has been created and successfully verified, you can use the [Payment Accounts](../api-reference/payment-accounts.md) endpoint to create a new payment account of type `FERN_AUTO_FIAT_ACCOUNT` for the customer. \
\
The destination payment account provided at creation must be a crypto wallet belonging to the customer that they wish any funds to settle into.\
\
The response should look like the below and contain the account details necessary to make transfers to the account on the payment rails available.

#### Response

```json
{
  "paymentAccountId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
  "paymentAccountType": "FERN_AUTO_FIAT_ACCOUNT",
  "nickname": "USD onramp Account",
  "createdAt": "2025-07-09T08:15:29.184Z",
  "customerId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
  "fernAutoFiatAccount": {
    "bankAccountOwnerName": "John Doe",
    "bankName": "Chase Bank",
    "bankAccountCurrency": "USD",
    "bankAccountPaymentMethods": [
      "ACH",
      "WIRE"
    ],
    "bankAccountNumber": "12346789",
    "routingNumber": "12346789",
    "iban": "GB29NWBK60161331926819",
    "bicSwift": "SMCOGB2LXXX",
    "destinationPaymentAccountId": "03b7030f-6da1-4d76-9352-cdebd82112c8",
    "destinationCurrency": "USDC"
  }
}
```
{% endstep %}

{% step %}
### Deposit funds&#x20;

At any time of your choosing send funds to the account via the account details listed using one of the allowed payment methods. This part does not require interaction with the Fern API and can be done directly from the customer bank account.\
\
These funds will automatically settle into the destination payment account and currency chosen at creation.
{% endstep %}

{% step %}
### Funds arrive

Funds should arrive into the account within the standard processing time of the payment rail used for deposit.\
\
Once the transaction is processing or completed a transaction will be created in the system to track the customer deposit.\
\
Track the progress of a transaction by calling the [**Transactions**](../api-reference/transactions/#api-v0-transactions-transactionid) endpoint or visiting the [**Developer dashboard**](../overview/developer-dashboard.md).&#x20;

For a full list of transaction statuses, refer to [**Transaction Statuses**](../api-reference/transactions/introduction.md#transaction-statuses).

#### Request

{% tabs %}
{% tab title="cURL" %}
```
curl --location 'https://api.fernhq.com/transactions' \
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

fetch("https://api.fernhq.com/transactions", requestOptions)
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
conn.request("GET", "/transactions", payload, headers)
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
    "transactionStatus": "COMPLETED",
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
        "destinationAmount": "10.478",
    },
    "fees": {
        "feeCurrency": {
            "label": "USD"
        },
        "fernFee": {
            "feeAmount": "0",
            "feeUSDAmount": "0
        },
        "developerFee": {
            "feeAmount": "0",
            "feeUSDAmount": "0"
        }
    },
    "createdAt": "2025-04-08T20:30:09.129Z"
}

```


{% endstep %}
{% endstepper %}



