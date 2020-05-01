# BIP270 Payment Requests

## Overview

BIP270 requests are dynamically served and are a fully peer-to-peer. These requests are made by providing a direct URI, pointing to a webserver, which will serve transaction data to the customer. 

Here are the required steps for a successful BIP270 transaction:
- The customer engages the invoice URI.
- The requester sends data to construct the tx.
- The customer constructs and signs the tx and sends it to the requester.
- The requester accepts the tx, the tx is broadcast, and a successful state is returned to the customer. 

## Implementation

To start, you will need to generate an invoice URL and provide it to your customer. The URL must be formatted into a URI with the following format:

`pay:?r=https://<url>`

This URI can also be imbedded into a QR Code, so that it may be scanned.

Once the URI is engaged by the customer, they will fetch the tx template from your server.

That request, should be *responded* to by returning an *object* with the following fields:

| Name           | Expected Value                                                             |Type      |
| -------------- | ----------------------------------------------------------------------- |------
| network (**required**)        | bitcoin-sv                          |string     |
| expirationTimestamp        | unix timestamp                     |number     |
| creationTimestamp (**required**)       | unix timestamp                     |number     |
| paymentUrl (**required**) | URL to deliver signed tx to   |string     |
| memo  | any length is accepted, only first 25 chars accepted   |string     |
| outputs (**required**) | an array of outputs, each array object must contain "script" and "amount"   |array     |
| ├► script  | P2PKH script   |string     |
| └► amount  | output amount in satoshis   |number     |
| merchantData  | an object containing "avatarUrl" and "merchantName"  |Object     |
| ├► avatarUrl  | A URL for the logo of the requester's business or shop   |string     |
| └► merchantName  | The name of the requester's business or shop   |string     |

Once the customer makes a confirmation, they will send a response to the *paymentURL*, with the sign transaction:
`{ transaction: txhex }`

When the signed tx is recieved, you will check to see the transaction is valid (and optionally broadcast the tx as you like), and respond with:
`{ success: true }`


## Example 

We can use BIP270 requests to charge for a cup of coffee, plus a tip for the server.

We first present the customer with a invoice URL:
![QRCode](/../resources/images/bip270qr.png ':class=qrcode') 
<center><a href="pay:?r=http://142.93.157.92:3000/">Pay</a></center>

Once the customer engages the URL you send back a transaction template.

- **restaurant address**: 1EQtNGbqq2Zhpqhwaf9dCuAjvQz7eAWcHy
- **server address**: 12gqniN78jNfSVama77m3niPKTpdmb8QEF
- **amount**: 0.01533906
- **tip**: 0.00432145
- **merchant name**: Joe's Café
- **link to avatar**: https://bit.ly/3f1fMX7

```
{
  "network": "bitcoin-sv",
  "outputs": [{
    "script": "76a91419e92fd0ec80a7bc8681e845d4575c28c38d052488ac",
    "amount": 1223506,
  },
  {
    "script": "76a91412811318c9cec427051555adadaf3343bb8a125588ac",
    "amount": 310400,
  }],
  "creationTimestamp": 1588222180,
  "expirationTimestamp": 1596222180,
  "paymentUrl": "http://142.93.157.92:3000/paid",
  "memo": "this is for coffee",
  "merchantData": "{\"avatarUrl\":\"https://bit.ly/3f1fMX7\",\"merchantName\":\"Joes Café\"}",
}
```

The customer receives the tx template and is presented with a payment confirmation screen:

![Scanned](/../resources/images/simple-pr-scanned.png ':class=screenshot')

The customer swipes right to confirm the payment and you receive a signed txhex in the following format:
```
{ transaction: '01000000017189fa12d60f042a1f5d9a1be07ad9c687e70ca30a074f2eabc03f2168922104020000006b4830450221009564cbdbd7bcea04f4130808a83f0de03494a676b2376f8bf0e710aadc4dc41802203494a67a9bdc154af01a74dff57749e5bbf58f93f26641121544caf9d2aa3c7b41210388684af8a7efdceca76f68c3b7e303494a677445cd243859de17f8c237654bd2ffffffff0389030000000000001976a91412811318c9cec427051555adadaf3343bb8a125588ac8a030000000000001976a91419e92fd0ec80a7bc8681e845d4575c28c38d052488ac5a020000000000001976a914ea313e66ebd8acbe8ddeaaa0839deed2db982a2588ac00000000' }
```

The transaction is checked for validity, and your server response with the following:

`{ success: true }`



