# Simple Payment Requests

## Overview

Simple payment requests require a specific format, and accept two additional optional fields. Once a payment request is opened, the user's app will display the amount requested, and if provided, your company logo and name. 

## Implementation

There are four different fields you can utilize for your simple payment request.

| Name           | Description                                                             |      |
| -------------- | ----------------------------------------------------------------------- |------
| Address (**required**)        | Raw Bitcoin (BSV) address                          |     |
| BsvAmount (**required**)        | The amount of BSV being requested                     |     |
| MerchantName        | The name of the requester's business or shop                     |     |
| LinkToAvatar  | A URL for the logo of the requester's business or shop   |     |

To create a payment request, each field should be added to the following string:

`bitcoin:<address>?sv=&amount=<bsvAmount>&label=<merchantName>&avatarUrl=<linkToAvatar>`

>This request can be displayed as a **QR code**, or a **clickable URI** depending on the setting. Once the user opens your request, they will require in-app verification before they pay. 

## Example

We can use simple payment requests to charge for a cup of coffee.

- **Address** 1EQtNGbqq2Zhpqhwaf9dCuAjvQz7eAWcHy
- **BsvAmount** 0.01533906
- **MerchantName** Joe's Café
- **LinkToAvatar**: https://bit.ly/3f1fMX7

All the criteria can be inserted into the simple payment request template, resulting in the following string:

`"bitcoin:1EQtNGbqq2Zhpqhwaf9dCuAjvQz7eAWcHy?sv&amount=0.01533906&label=Joe's Café&avatarUrl=https://bit.ly/3f1fMX7"`

This can be turned into a [URI](bitcoin:1EQtNGbqq2Zhpqhwaf9dCuAjvQz7eAWcHy?sv&amount=0.01533906&label=Joe's20%Café&avatarUrl=https://bit.ly/3f1fMX7), or a QRCode:

![QRCode](/../resources/images/simple-pr-qrcode.png ':class=qrcode') 

The user can scan or open this payment request, resulting in the following payment confirmation screen:

![Scanned](/../resources/images/simple-pr-scanned.png ':class=screenshot')

