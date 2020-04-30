# Simple Payment Requests

## Overview

BIP270 requests are capable of dynamic transactions and they are 100% peer-to-peer. These requests are made by providing a direct payment URL. The URL will point to a webserver which will server the transaction data. 

## Implementation

A BIP270 GET request should respond with the following fields:

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

>This request can be displayed as a **QR code**, or a **clickable URI** depending on the setting. Once the user opens your request, they will require in-app verification before they pay. 

## Example

We can use BIP270 requests to charge for a cup of coffee.

- **addresses** 1EQtNGbqq2Zhpqhwaf9dCuAjvQz7eAWcHy, 12gqniN78jNfSVama77m3niPKTpdmb8QEF
- **amount** 0.01533906
- **merchant name** Joe's Café
- **link to avatar**: https://bit.ly/3f1fMX7

```
{
   "network" : "bitcoin-sv",
   "expirationTimestamp": "1596222180",
   "creationTimestamp" : "1588222180",
   "paymentUrl" : "https://<url>",
   "outputs" : [{
      "script" : "1EQtNGbqq2Zhpqhwaf9dCuAjvQz7eAWcHy",
      "amount" : "1000000",
   },
   {
      "script" : "12gqniN78jNfSVama77m3niPKTpdmb8QEF",
      "amount" : "533906",
   }],
   "memo" : "this is for coffee",
   "merchantData" : {
      "avatarUrl" : "https://bit.ly/3f1fMX7",
      "merchantName" : "Joe's Café"
   },
}
```

This can be turned into a [URI](bitcoin:1EQtNGbqq2Zhpqhwaf9dCuAjvQz7eAWcHy?sv&amount=0.01533906&label=Joe's20%Café&avatarUrl=https://bit.ly/3f1fMX7), or a QRCode:

![QRCode](/../resources/images/simple-pr-qrcode.png ':class=qrcode') 

The user can scan or open this payment request, resulting in the following payment confirmation screen:

![Scanned](/../resources/images/simple-pr-scanned.png ':class=screenshot')

