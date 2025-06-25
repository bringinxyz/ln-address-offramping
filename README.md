# Bringin LN Address Offramping Integration Guide

## Overview

Bringin allows **payment providers, merchant platforms, and Lightning service providers** to offer seamless *Bitcoin-to-Euro offramping* using Lightning Addresses.

A Lightning Address looks like an email address — e.g.: `merchant@bringin.xyz`

When sats are sent to this address, they are converted into Euros and credited to the merchant's **vIBAN account** inside Bringin. The funds are:

✅ Instantly spendable via **physical/virtual debit card**  
✅ Withdrawable via **instant SEPA bank transfer**

## Key Benefits for Payment Providers

* Provide **Bitcoin → Euro offramping** directly from Lightning payments
* No banking integration or card program needed on your side
* Works with your existing Lightning stack (custodial or non-custodial)
* Bringin handles conversion, compliance, settlement
* Merchants can spend Euros via card or withdraw to bank

## What is a Lightning Address?

A Lightning Address is a **human-readable identifier** that looks like an email, but works as a payment address on the Lightning Network.

Example: `merchant@bringin.xyz`

Internally, it is built on **LNURL-Pay**, allowing wallets or services to retrieve payment parameters and send Lightning payments.

## How It Works

1️⃣ The payment provider resolves the LN address (LNURL-Pay spec)  
2️⃣ Sends a Lightning payment to the generated invoice  
3️⃣ Bringin converts received BTC to EUR  
4️⃣ EUR is credited to merchant's vIBAN  
5️⃣ Merchant can spend using debit card or withdraw via SEPA Instant

## Default Limits

* ⚠️ Default **minSendable** is €10 worth of sats (because of exchange partner limits)

This works well when users send funds manually from their wallet by typing in the LN address.

## POS / Micro-transaction Use Case

For **small payments** (e.g. 100 sats), we support a special mode:

Add `?pos=true` to the LNURL request:

```bash
https://bringin.xyz/.well-known/lnurlp/merchant?pos=true
```

* **minSendable**: 100 sats (10000 millisats)

## LNURL-Pay Example Response

```json
{
  "status": "OK",
  "tag": "payRequest",
  "callback": "https://bringin.xyz/lnurlp/merchant/callback",
  "metadata": "[[\"text/plain\",\"Payment to merchant@bringin.xyz\"]]",
  "commentAllowed": 120,
  "minSendable": "100000", // 100 sats
  "maxSendable": 5471036000,
  "payerData": {
    "name": {
      "mandatory": false
    },
    "email": {
      "mandatory": false
    },
    "pubkey": {
      "mandatory": false
    }
  }
}
```

## How to Integrate Using JS Library

You can easily enable LN address payments in your app using: 📦 lnurl-pay

### Install

```bash
npm install lnurl-pay
```

### Basic Example

```js
const lnurlPay = require('lnurl-pay');

(async () => {
  const lnAddress = 'merchant@bringin.xyz';
  const lnurlData = await lnurlPay.requestInvoice({
    lnUrlOrAddress: lnAddress
  });
  
  console.log('Invoice:', lnurlData.invoice);
  console.log('Callback URL:', lnurlData.callback);
  console.log('Min sendable:', lnurlData.minSendable);
})();
```

### Example with POS Mode (low minSendable)

```js
const lnurlPay = require('lnurl-pay');

(async () => {
  const lnAddress = 'merchant@bringin.xyz';
  const lnurlWithPOS = 'https://bringin.xyz/.well-known/lnurlp/merchant?pos=true';
  
  const lnurlData = await lnurlPay.requestInvoice({
    lnUrlOrAddress: lnurlWithPOS
  });
  
  console.log('Invoice for POS:', lnurlData.invoice);
})();
```

## Testing

* LN address: `sandbox-merchant@bringin.xyz` *(Testnet)*
* Live merchant LN address: `merchant@bringin.xyz`

## Support

If you need help integrating, contact: 📧 support@bringin.xyz
