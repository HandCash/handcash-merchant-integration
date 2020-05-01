# Legacy Payment Requests

## Overview

Legacy payment requests require a specific format, and accept two optional fields. Once a payment request is opened, the customer's app will display the amount requested along with the avatar and name provided. 

## Implementation

There are four different fields you can utilize for your legacy payment request.

| Name           | Description                                                             |      |
| -------------- | ----------------------------------------------------------------------- |------
| address (**required**)        | Raw Bitcoin (BSV) address                          |     |
| amount (**required**)        | The amount of BSV being requested                     |     |
| label        | The name of the requester's business or shop                     |     |
| avatarUrl  | A URL for the logo of the requester's business or shop   |     |

To create a payment request, each field should be added to the following string:

`bitcoin:<address>?sv=&amount=<amount>&label=<name>&avatarUrl=<avatar>`

>This request can be displayed as a **QR code**, and or a **clickable URI** depending on the setting. Once the customer opens your request, they will require in-app verification to pay. 

## Example

Legacy payment requests can be used to charge for a cup of coffee.

- **address:** 1EQtNGbqq2Zhpqhwaf9dCuAjvQz7eAWcHy
- **amount:** 0.01533906
- **name:** Joe's Café
- **avatar:** https://bit.ly/3f1fMX7

The merchant inserts all criteria into the legacy payment request template, resulting in the following URI:

`"bitcoin:1EQtNGbqq2Zhpqhwaf9dCuAjvQz7eAWcHy?sv&amount=0.01533906&label=Joe's Café&avatarUrl=https://bit.ly/3f1fMX7"`

This URI can also be displayed as a QRCode:

![QRCode](/../resources/images/legacy-pr-qrcode.png ':class=qrcode') 

<center><a href="bitcoin:1EQtNGbqq2Zhpqhwaf9dCuAjvQz7eAWcHy?sv&amount=0.01533906&label=Joe's20%Café&avatarUrl=https://bit.ly/3f1fMX7">Pay</a></center>

The customer opens this payment request, resulting in the following payment confirmation screen:

![Scanned](/../resources/images/legacy-pr-scanned.png ':class=screenshot')

Once the customer's intent to pay is confirmed, they will broadcast a corresponding transaction to the blockchain.