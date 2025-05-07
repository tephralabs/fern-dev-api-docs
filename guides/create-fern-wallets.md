# Create Fern wallets for your customers

## Fern wallets

Fern enables you to create wallets for your customers on all Fern [supported chains](../cryptocurrency-support.md). Currently, on EVM chains, Fern creates wallets as Safe multisig wallets with a 1-of-1 multisig signing threshold and a Fireblocks MPC EOA configured as the signer. These wallets can hold any valid token on any supported chain, although not all tokens may be supported for currency conversion. To determine which currencies are supported for conversion, you can use the [Quotes API](../api-reference/quotes.md).&#x20;

Fern wallets are primarily used to hold cryptocurrencies either for onramping or offramping, and less so for peer-to-peer payments. If p2p payments and sends are required for your use case, please reach out to [support@fernhq.com](mailto:support@fernhq.com).

## Overview

To create Fern wallets for your customers, customers need to be created via the [Customers API](../api-reference/customers.md). Your end customer does not need to be `ACTIVE` (approved) in order to have an active Fern wallet. As soon as the customer has been created and you have the customer's ID, you can use this to create a Fern wallet on the relevant chain(s).

## Step-by-step guide

{% stepper %}
{% step %}
### Create your customer

Create a customer using the [Customers API](../api-reference/customers.md). Specify customer type, name, and email address. A successful request will return a response that includes the `customerId`.
{% endstep %}

{% step %}
### Create Fern wallet for the customer

Fern wallets can be created using the [Payment accounts API](../api-reference/payment-accounts.md). Specify `paymentAccountType` as `FERN_CRYPTO_WALLET` and include the `customerId` in the request. Because you are creating a Fern crypto wallet, you also need to send the `fernCryptoWallet` object with `cryptoWalletType`. Today, Fern only supports EVM wallets for creation, so the correct `cryptoWalletType`  will be `EVM`. This will create a Fern crypto wallet that has the same address on all supported EVM chains.

**Sample request body:**

```json
{
  "paymentAccountType": "FERN_CRYPTO_WALLET",
  "customerId": "4d0b92ee-b48f-471f-8825-61761c0c6eb7",
  "fernCryptoWallet": {
    "cryptoWalletType": "EVM"
  }
}
```

**Sample request response:**

```json
{
  "paymentAccountType": "FERN_CRYPTO_WALLET",
  "paymentAccountId": "238e98ce-2305-57e0-83f1-c3286e7db76c",
  "createdAt": "2025-05-07T13:55:06.697Z",
  "fernCryptoWallet": {
    "cryptoWalletType": "EVM",
    "address": "0xb9a71467bc058c744b07285ec12533ac88095270"
  },
  "isThirdParty": false
}
```
{% endstep %}

{% step %}
### Fern crypto wallet is now live

This wallet can now be used as the destination for onramps, or a source for offramps. If you plan to use this wallet to interact with other Fern APIs, keep track of the returned `paymentAccountId` .
{% endstep %}
{% endstepper %}

