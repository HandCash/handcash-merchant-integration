# Merchant Payment Requests

## Overview

Merchant requests use the bip270 protocol, meaning they can be dynamically served, and are fully peer-to-peer. These requests are made by providing a direct URI, pointing to a web server, which will serve transaction data to the customer. 

Here are the required steps for a successful Merchant transaction:
- The merchant displays an invoice URI.
- The customer engages the invoice URI.
- The merchant sends data to construct the tx.
- The customer constructs, signs, and sends the tx to the merchant.
- The merchant validates the tx, broadcasts the tx, then returns a successful state to the customer.
- The customer receives the successful response, and attempts to broadcast the tx as well.

![Scanned](/../resources/images/merchant-flow.png ':class=flowchart')

## Implementation

To start, you will need to generate an invoice URL and provide it to your customer. Use this URL to generate a URI with the following format:

`pay:?r=<url>`

This URI can be embedded into a QR Code, so that it can be scanned.

Once the URI is engaged by your customer, they will fetch the transaction template from your server.

You should respond to that request with an *object* that meets the following criteria:

| Name           | Expected Value                                                             |Type      |
| -------------- | ----------------------------------------------------------------------- |------
| network (**required**)        | bitcoin-sv                          |string     |
| outputs (**required**) | an array of outputs, each array object must contain "script" and "amount"   |array     |
| ├► script  | P2PKH script   |string     |
| └► amount  | output amount in satoshis   |number     |
| creationTimestamp (**required**)       | unix timestamp                     |number     |
| expirationTimestamp        | unix timestamp                     |number     |
| paymentUrl (**required**) | URL to deliver signed tx to   |string     |
| memo  | any length (first 25 characters appear as note)   |string     |
| merchantData  | an object containing "avatarUrl" and "merchantName"  |Object     |
| ├► avatarUrl  | A URL for the logo of the merchant's business or shop   |string     |
| └► merchantName  | The name of the merchant's business or shop   |string     |

Once the customer receives this transaction template, they will be prompted to confirm their intent to pay. Once confirmed, they will send a response to the *paymentURL*, with a sign transaction:

`{ transaction: txhex }`

When the signed transaction is received, you will check to see if the transaction is valid. If valid, you may broadcast it, and respond with:

`{ success: true }`


## Examples 

### Example 1

Merchant requests can be used to charge for a cup of coffee, plus a tip for the waiter.

The merchant must first present the customer with an invoice URI:

![QRCode](/../resources/images/merchant-qr.png ':class=qrcode') 

<center><a href="pay:?r=http://142.93.157.92:3000/">Pay</a></center>

Once the customer engages the URI, the merchant will send back a transaction template.

- **restaurant address**: 1EQtNGbqq2Zhpqhwaf9dCuAjvQz7eAWcHy
- **waiter address**: 12gqniN78jNfSVama77m3niPKTpdmb8QEF
- **amount**: 0.01533906
- **tip**: 0.00432145
- **merchant name**: Joe's Café
- **link to avatar**: https://bit.ly/3f1fMX7

```
{
  "network": "bitcoin-sv",
  "outputs": [{
    "script": "76a91419e92fd0ec80a7bc8681e845d4575c28c38d052488ac",
    "amount": 1533906,
  },
  {
    "script": "76a91412811318c9cec427051555adadaf3343bb8a125588ac",
    "amount": 432145,
  }],
  "creationTimestamp": 1588222180,
  "expirationTimestamp": 1596222180,
  "paymentUrl": "http://142.93.157.92:3000/paid",
  "memo": "this is for coffee",
  "merchantData": "{\"avatarUrl\":\"https://bit.ly/3f1fMX7\",\"merchantName\":\"Joes Café\"}",
}
```

The customer will receive the transaction template, and be presented with a payment confirmation screen:

![Scanned](/../resources/images/merchant-scanned-ex1.png ':class=screenshot')

The customer swipes right to confirm the payment and the merchant receives a signed txhex in the following format:
```
{ transaction: '01000000017189fa12d60f042a1f5d9a1be07ad9c687e70ca30a074f2eabc03f2168922104020000006b4830450221009564cbdbd7bcea04f4130808a83f0de03494a676b2376f8bf0e710aadc4dc41802203494a67a9bdc154af01a74dff57749e5bbf58f93f26641121544caf9d2aa3c7b41210388684af8a7efdceca76f68c3b7e303494a677445cd243859de17f8c237654bd2ffffffff0389030000000000001976a91412811318c9cec427051555adadaf3343bb8a125588ac8a030000000000001976a91419e92fd0ec80a7bc8681e845d4575c28c38d052488ac5a020000000000001976a914ea313e66ebd8acbe8ddeaaa0839deed2db982a2588ac00000000' }
```

The transaction is checked for validity, the merchant chooses to broadcast the transaction and responds with the following:

`{ success: true }`

The transaction is seen as succesful, the user also broadcasts the transaction and records it.

### Example 2

Merchant request URIs are dynamic, meaning the URI can remain static while the merchant returns content dymanically. This means the merchant doesn't nessesarily need to change the QR code to update the price of their coffee (they can update the transaction at any point, before it's scanned).

As such, the merchant can use the same URI as in *Example 1*:

![QRCode](/../resources/images/merchant-qr.png ':class=qrcode') 

<center><a href="pay:?r=http://142.93.157.92:3000/">Pay</a></center>

Once the customer engages the URI, the merchant generates his response, sending back a transaction template.

Here, the merchant operates in USD, so they check the exchange rate before sending a response. The exchange rate of BSV has increased by 5% since the URI was generated, so the merchant chooses to automatically decreases the amount of BSV requested, to reflect that. The merchant also updates the addresses for this transaction, as the earlier addresses have been used. 

The marchant also decides that he will designate this URI for a specific item: one medium cup of coffee, instead of displaying the details for his shop.

- **restaurant address**: 14HYHGtvEwEuqRtDFhQ3goKFoV4zpMuuoq
- **waiter address**: 1MCXRCxYHDHHVUZkH5SzjjXHXBkCBhB4Ux
- **amount**: 0.014572107
- **tip**: 0.004105377
- **merchant name**: Medium Coffee 12oz.
- **link to avatar**: https://bit.ly/3c4iaup

```
{
  "network": "bitcoin-sv",
  "outputs": [{
    "script": "76a914240924fb45ff07ea1aab22e9b57847dedd80255388ac",
    "amount": 14572107,
  },
  {
    "script": "76a914dd907b7d2658ef12122068e269eda3f37a5bb39588ac",
    "amount": 4105377,
  }],
  "creationTimestamp": 1588222180,
  "expirationTimestamp": 1596222180,
  "paymentUrl": "http://142.93.157.92:3000/paid",
  "memo": "this is for coffee",
  "merchantData": "{\"avatarUrl\":\"https://bit.ly/3c4iaup\",\"merchantName\":\"Medium Coffee 12oz.\"}",
}
```

The customer will receive the transaction template, and be presented with a payment confirmation screen:

![Scanned](/../resources/images/merchant-scanned-ex2.png ':class=screenshot')

The customer swipes right to confirm the payment and the merchant receives a signed txhex in the following format:
```
{ transaction: '01000000017189fa12d60f042a1f5d9a1be07ad9c687e70ca30a074f2eabc03f2168922104020000006b4830450221009564cbdbd7bcea04f4130808a83f0de03494a676b2376f8bf0e710aadc4dc41802203494a67a9bdc154af01a74dff57749e5bbf58f93f26641121544caf9d2aa3c7b41210388684af8a7efdceca76f68c3b7e303494a677445cd243859de17f8c237654bd2ffffffff0389030000000000001976a91412811318c9cec427051555adadaf3343bb8a125588ac8a030000000000001976a91419e92fd0ec80a7bc8681e845d4575c28c38d052488ac5a020000000000001976a914ea313e66ebd8acbe8ddeaaa0839deed2db982a2588ac00000000' }
```

The transaction is checked for validity, the merchant chooses to broadcast the transaction and responds with the following:

`{ success: true }`

The transaction is seen as succesful, the user also broadcasts the transaction and records it.



