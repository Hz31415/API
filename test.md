<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


# ZAM.IO API



  - [General API Information](#general-api-information)
  - [HTTP Return Codes](#http-return-codes)
  - [Error Codes](#error-codes)
  - [Common response parts](#common-response-parts)
- [Wallet management API](#wallet-management-api)
  - [Account management requests](#account-management-requests)
    - [New user registration](#new-user-registration)
    - [Password change](#password-change)
    - [Request for security agreements](#request-for-security-agreements)
    - [Delete account](#delete-account)
    - [Generate private key](#generate-private-key)
    - [Update token](#update-token)
  - [Order requests](#order-requests)
    - [Request for transaction order generation](#request-for-transaction-order-generation)
    - [Order for cryptocurrency buy](#order-for-cryptocurrency-buy)
    - [Order for cryptocurrency sell](#order-for-cryptocurrency-sell)
  - [Enable or Disable external wallets](#enable-or-disable-external-wallets)
    - [Enable external wallet](#enable-external-wallet)
    - [Disable external wallet](#disable-external-wallet)
  - [Request for rate information](#request-for-rate-information)
    - [Get top50 cryptocurrencies' information](#get-top50-cryptocurrenies-information)
    - [Selected cryptocurrency to dollar rate](#selected-cryptocurrency-to-dollar-rate)
    - [Selected cryptocurrency to Zam rate](#selected-cryptocurrency-to-zam-rate)
  - [Amount information](#amount-information)
    - [Balance check on all connected wallets](#balance-check-on-all-connected-wallets)
  - [History](#history)
    - [Transaction history for selected wallet](#transaction-history-for-selected-wallet)
  - [Notifications management](#notifications-management)
    - [Update notifications](#update-notifications)
  - [Calculation requests](#calculation-requests)
    - [Transaction information request](#transaction-information-request)

# General API information
The Zam.io API that serves for creating and managing Your wallet:
* Registration and account management
* Cryptocurrency buy/sell orders
* Working with external wallets
* Ability to update rate information

# Common response parts

The response always consist of two objects `meta` and `data`.

The `meta` object has two parameters `status` that could be `"OK"` or `"ERROR"` and `message` with status describtion values: `"SUCCESS"` `"Not found"` and etc..
`data` object will contain either no object or response's payload.

# Wallet management API
The endpoint stands for wallet management as account administration, ordering transactions, adding/deleting external wallets and etc..
## Account management requests
### New User registration

New user registration request. 


Request type and URL:
```
POST https://zam.io/api/auth
```

Request parameters:

Name | Type | Description
------------ | ------------ | ------------
phone | STRING | User’s phone number. Should contain at least 10 characters.
fullName | STRING | User’s full name(Surname, Name, Patronymic/Middle Name).
refCode | STRING | The referral code that user received if was invited.
codeRequest | OBJECT | Subrequest for number verification. Contains “phone” field that was mentioned above.


Request example:
```json
    {
      "phone": "0647744360",
      "fullName": "tempor laboris ipsum esse",
      "refCode": "ullamco",
      "codeRequest": 
      {
       "phone": "62038729829242"
      }
    }
```    

Response parameters:

Name | Type | Description
------------ | ------------ | ------------
passportMessage | STRING | Passport phrase.
agreement | OBJECT | An `agreement` object that consist of parameters describet below.
id | STRING | UUID of agreement document. Example: `173e4567-e89b-12d3-a456-426624174000`.
name  | STRING | The agreement file name.
url  | STRING | The agreement file's URL.


Response example:

```json
    {
     "meta": 
     {
      "status": "OK",
      "message": "Success"
     },
     "data": 
     {
      "passportMessage": "adipisicing in",
      "agreement": 
      {
       "id": "173e4567-e89b-12d3-a456-426624174000",
       "name": "Security agreement",
       "url": "/api/auth/documents/173e4567-e89b-12d3-a456-426614174000"
      }
     }
    }
```

### Password change
Simple password change request.

Request type and URL:
```
POST https://zam.io/api/auth/change
```

Request parameters:

Name | Type | Description
------------ | ------------ | ------------
oldPassword | STRING | Old password's hash.
newPassword | STRING | New password's hash.

Request example:
```json
    {
     "oldPassword": "ut proident",
     "newPassword": "sit laborum proident nisi do"
    }
```

Response example:
```json
    {
     "meta": 
     {
      "status": "OK",
      "message": "Success"
     }
    }
```
### Request for security agreements
Security agreement for user to accept.

Request type and URL:

```
 GET https://zam.io/api/auth/documents/:documentId
```
The response returns security agreement file to download.

### Delete account
Request to delete authorized account.

Request type and URL:
```
DELETE https://zam.io/api/auth/delete
```
Response example:

```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {}
}
```
### Generate private key
Request for Zam wallet's private key generation

Request type and URL:
```
GET https://zam.io/api/auth/privateKey
```

Response parameters:

Name | Type | Description
------------ | ------------ | ------------
privateKey | STRING | Zam wallet's private key.

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {
  "privateKey": "sint ut mollit occaecat dolore"
 }
}
```
### Update token
Request for authorization token. 

Request type and URL:
```
POST https://zam.io/api/auth/token
```

Request parameters:

Name | Type | Description
------------ | ------------ | ------------
token | STRING | Last expired token.
deviceId | STRING | Device’s UUID.
login | STRING | User’s login.
password | STRING | Hash of user’s password.

Request example:
```json
{
    "token": "irure ut eiusmod in",
    "deviceId": "amet d",
    "login": "velit consectetur ut irure",
    "password": "in ut et"
}
```

Response parameters:

Name | Type | Description
------------ | ------------ | ------------
token | STRING | Last expired token.
agreement | OBJECT | An object with three parameters described.
id | STRING | UUID of agreement document. Example: `173e4567-e89b-12d3-a456-426624174000`.
name  | STRING | The agreement file name.
url  | STRING | The agreement file's URL.


Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {
  "token": "esse ullamco amet nostrud",
  "agreement": {
   "id": "173e4567-e89b-12d3-a456-426624174000",
   "name": "Security agreement",
   "url": "/api/auth/documents/173e4567-e89b-12d3-a456-426614174000"
  }
 }
}
```

## Order requests
### Request for transaction order generation

Request for transaction order to buy/sell cryptocurrency. Headers of request are described below
Here should be table

Request parameters:

Name | Type | Description
------------ | ------------ | ------------
priority | STRING | Transaction priority (default value - MEDIUM, optional - TOP).
blockchainFrom | STRING | Source platform name.
blockchainTo | STRING | Destination platform name.
addressContractFrom | STRING | Contract adrress on source wallet.
addressContractTo | STRING | Contract address on destiantion wallet.
adressFrom | STRING | Soruce wallet address.
addressTo | STRING | Destionation wallet address.
currency | STRING | Cryptocurrency shortname.
amount | STRING | Cryptrocurrency amount.
dateCreate | STRING | Order creation date:time.

Request type and URL:
```
GET https://zam.io/api/order?priority=MEDIUM&blockchainFrom=doadipisicing&blockchainTo=doadipisicing&addressContractFrom=doadipisicing&addressContractTo=doadipisicing&adressFrom=doadipisicing&addressTo=doadipisicing&amount=doadipisicing&paymentRestrictionTransaction=doadipisicing
```

Response parameters:

Name | Type | Description
------------ | ------------ | ------------
blockchainFrom | STRING | Source platform name.
blockchainTo | STRING | Destination platform name.
addressTo | STRING | Destionation wallet address.
amount | STRING | Cryptrocurrency amount.
dateCreate | STRING | Order creation date:time.
addressContractFrom | STRING | Contract adrress on source wallet.
addressContractTo | STRING | Contract address on destiantion wallet.
adressFrom | STRING | Soruce wallet address.
currency | STRING | Cryptocurrency shortname.

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {
  "blockchainFrom": "non consequat",
  "blockchainTo": "laboris aute velit",
  "addressTo": "veniam magna sunt",
  "amount": "veniam qui enim Excepteur",
  "dateCreate": "deserunt aliquip",
  "addressContractFrom": "est laboris aliqua ut do",
  "addressContractTo": "ex velit",
  "adressFrom": "ad",
  "currency": "officia ut deserunt ullamco"
 }
}
```
### Order for cryptocurrency buy
The buy operation that follows after receiving transaction order.

Request parameters:

Name | Type | Description
------------ | ------------ | ------------
priority | STRING | Transaction priority (default value - MEDIUM, optional - TOP).
blockchainTo | STRING | Destination platform name.
addressContractTo | STRING | Contract address on destiantion wallet.
addressTo | STRING | Destionation wallet address.
currency | STRING | Cryptocurrency shortname.
amount | STRING | Cryptrocurrency amount.
dateCreate | STRING | Order creation date:time.
order | STRING | Reference to transaction order for payment.

Request type and URL:
```
PUT https://zam.io/api/order/purchase?priority=MEDIUM&blockchainTo=doadipisicing&addressContractTo=doadipisicing&addressTo=doadipisicing&amount=doadipisicing&paymentRestrictionTransaction=doadipisicing
```

Response parameters:

Name | Type | Description
------------ | ------------ | ------------
blockchainTo | STRING | Destination platform name.
addressTo | STRING | Destionation wallet address.
amount | STRING | Cryptrocurrency amount.
dateCreate | STRING | Order creation date:time.
order | STRING | Reference to transaction order for payment.
addressContractTo | STRING | Contract address on destiantion wallet.
currency | STRING | Cryptocurrency shortname.

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {
  "blockchainTo": "occaecat ea Duis cillum",
  "addressTo": "culpa consequat",
  "amount": "laboris irure amet",
  "dateCreate": "quis elit non",
  "order": "aute culpa",
  "addressContractTo": "irure commodo Duis in",
  "currency": "labore ut Ut anim"
 }
}
```
### Order for cryptocurrency sell
The sell operation that follows after receiving transaction order.
FPS - Fast Payment System

Request type and URL:
```
PUT https://zam.io/api/order/sale?priority=MEDIUM&blockchainFrom=doadipisicing&blockchainTo=doadipisicing&addressContractFrom=doadipisicing&addressContractTo=doadipisicing&adressFrom=doadipisicing&addressTo=doadipisicing&amount=doadipisicing&paymentRestrictionTransaction=doadipisicing
```

Request example:
```json
{
    "spbPhone1": "dolore qui cillum",
    "spbName1": "dolor nulla laboris esse do",
    "visaCardNumber2": "dolore minim commodo",
    "visaName2": "do fugiat",
    "masterCardNumber3": "et nisi minim quis",
    "masterName3": "irure",
    "bankAccountPaymentAccount4": "ad",
    "bankAccountBIK4": "dolor aliquip cillum exercitation",
    "bankAccountName4": "quis"
}
```

Response parameters:

Name | Type | Description
------------ | ------------ | ------------
priority | STRING | Transaction priority (default value - MEDIUM, optional - TOP).
blockchainFrom | STRING | Source platform name.
blockchainTo | STRING | Destination platform name.
addressTo | STRING | Destionation wallet address.
amount | STRING | Cryptrocurrency amount.
dateCreate | STRING | Order creation date:time.
addressContractFrom | STRING | Contract adrress on source wallet.
addressContractTo | STRING | Contract address on destiantion wallet.
adressFrom | STRING | Soruce wallet address.
currency | STRING | Cryptocurrency shortname.

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {
  "blockchainFrom": "non consequat",
  "blockchainTo": "laboris aute velit",
  "addressTo": "veniam magna sunt",
  "amount": "veniam qui enim Excepteur",
  "dateCreate": "deserunt aliquip",
  "addressContractFrom": "est laboris aliqua ut do",
  "addressContractTo": "ex velit",
  "adressFrom": "ad",
  "currency": "officia ut deserunt ullamco"
 }
}
```
## Enable or Disable external wallets

### Enable external wallet

Request for adding external wallet.

Request type and URL:
```
POST https://zam.io/api/otherCrypto
```

Request parameters:

Name | Type | Description
------------ | ------------ | ------------
blockchain | STRING | Platform name.
addressContract | STRING | Wallet’s contract address.
adress | STRING | Wallet's adress.

Request example:
```json
{
 "blockchain": "officia laboris",
 "addressContract": "proident in ut",
 "adress": "et esse"
}
```

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {}
}
```

### Disable external wallet
Request to disable external wallet. Headers are described below.

Request parameters:

Name | Type | Description
------------ | ------------ | ------------
blockchain | STRING | Platform name.
addressContract | STRING | Wallet’s contract address.
adress | STRING | Wallet's adress.

Request type and URL:
```
DELETE https://zam.io/api/otherCrypto?blockchain=doadipisicing&addressContract=doadipisicing&address=doadipisicing
```

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {}
}
```

## Request for rate information

### Get top50 cryptocurrenies' information
Request for top 50 crypto currencies with currency to Zam/dollar rate.

Request type and URL:
```
GET https://zam.io/api/rate
```

Response is an object array of currencies' describtions and rate data. Parameters:

Name | Type | Description
------------ | ------------ | ------------
blockchain | STRING | Platform name.
address | STRING | Wallet's adress.
currency | STRING | Cryptocurrency shortname.
rateToDollar | STRING | Rate to dollar.
rateToZam | STRING | Rate to Zam.
amount | STRING | Cryptrocurrency amount.
dateCreate | STRING | Date:time of exchange rate actualization.
addressContract | STRING | Wallet’s contract address.


Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": [
  {
   "blockchain": "consequat fugiat",
   "address": "quis eiusmod Duis nostrud dolore",
   "currency": "ad nulla officia quis",
   "rateToDollar": "sint officia consectetur sed",
   "rateToZam": "ad Excepteur est",
   "amount": "commodo id aliquip",
   "dateRate": "adipisicing labore irure dolore",
   "addressContract": "dolor"
  },
  {
   "blockchain": "do nisi",
   "address": "irure Lorem in in",
   "currency": "esse anim consectetur in sit",
   "rateToDollar": "quis officia aute",
   "rateToZam": "irure voluptate pariatur",
   "amount": "occaecat laborum aliqua amet Lorem",
   "dateRate": "voluptate occ",
   "addressContract": "Duis eiusmod"
  }
 ]
}
```

### Selected cryptocurrency to dollar rate
Gets rate to dollar. Headers are described below.

Request parameters:

Name | Type | Description
------------ | ------------ | ------------
currency | STRING | Cryptocurrency shortname.
blockchain | STRING | Platform name.
addressContract | STRING | Wallet’s contract address.

Request type and URL:
```
GET https://zam.io/api/rate/dollar?currency=doadipisicing&blockchain=doadipisicing&addressContract=doadipisicing
```

Response parameters:

Name | Type | Description
------------ | ------------ | ------------
blockchain | STRING | Platform name.
address | STRING | Wallet's adress.
currency | STRING | Cryptocurrency shortname.
rateToDollar | STRING | Rate to dollar.
rateToZam | STRING | Rate to Zam.
amount | STRING | Cryptrocurrency amount.
dateCreate | STRING | Date:time of exchange rate actualization.
addressContract | STRING | Wallet’s contract address.

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {
  "blockchain": "sed non",
  "address": "incididunt mollit",
  "currency": "aliquip quis ut",
  "rateToDollar": "eu ad",
  "rateToZam": "commodo qui sint",
  "amount": "sunt esse minim",
  "dateRate": "Ut minim sunt Duis",
  "addressContract": "n"
 }
}
```

### Selected cryptocurrency to Zam rate
Gets rate to Zam. Headers are described below.

Request parameters:

Name | Type | Description
------------ | ------------ | ------------
currency | STRING | Cryptocurrency shortname.
blockchain | STRING | Platform name.
addressContract | STRING | Wallet’s contract address.

Request type and URL:
```
GET https://zam.io/api/rate/zam?currency=doadipisicing&blockchain=doadipisicing&addressContract=do dipisicing
```

Response parameters:

Name | Type | Description
------------ | ------------ | ------------
blockchain | STRING | Platform name.
address | STRING | Wallet's adress.
currency | STRING | Cryptocurrency shortname.
rateToDollar | STRING | Rate to dollar.
rateToZam | STRING | Rate to Zam.
amount | STRING | Cryptrocurrency amount.
dateCreate | STRING | Date:time of exchange rate actualization.
addressContract | STRING | Wallet’s contract address.

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {
  "blockchain": "sed non",
  "address": "incididunt mollit",
  "currency": "aliquip quis ut",
  "rateToDollar": "eu ad",
  "rateToZam": "commodo qui sint",
  "amount": "sunt esse minim",
  "dateRate": "Ut minim sunt Duis",
  "addressContract": "n"
 }
}
```
## Amount information

### Balance check on all connected wallets
Request for balance information on all enabled wallets.

Request type and URL:
```
GET https://zam.io/api/amount
```

Request is an array of objects that describes wallet data. Parameters:

Response parameters:

Name | Type | Description
------------ | ------------ | ------------
blockchain | STRING | Platform name.
address | STRING | Wallet's adress.
currency | STRING | Cryptocurrency shortname.
amount | STRING | Cryptrocurrency amount.
addressContract | STRING | Wallet’s contract address.

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": [
  {
   "blockchain": "consequat est et anim",
   "address": "voluptate incididunt consectetur",
   "currency": "et mollit deserunt",
   "amount": "irure do non",
   "addressContract": "do"
  },
  {
   "blockchain": "eiusmod amet sed dolor",
   "address": "magna do amet",
   "currency": "labore",
   "amount": "eu irure dolore magna",
   "addressContract": "in esse aute"
  }
 ]
}
```

## History

### Transaction history for selected wallet

Request type and URL:
```
GET https://zam.io/api/history
```

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": [
  {
   "blockchainFrom": "officia velit",
   "blockchainTo": "Ut culpa mollit in",
   "addressTo": "proident Lorem",
   "amount": "cupidatat commodo dolor sed",
   "commission": [
    {
     "paymentTransaction": "irure sed",
     "commissionTransaction": "laborum irure molli"
    },
    {
     "paymentTransaction": "minim",
     "commissionTransaction": "esse consectetur fugiat officia ullamco"
    }
   ],
   "dateCreate": "qui enim sunt culpa",
   "dateConfirm": "amet est dolor veniam",
   "addressContractFrom": "commodo",
   "addressContractTo": "incididunt co",
   "adressFrom": "incididunt irure",
   "currency": "dolore in deserunt non"
  },
  {
   "blockchainFrom": "in do",
   "blockchainTo": "ut commodo ullamco",
   "addressTo": "qui ut",
   "amount": "do laborum tempor",
   "commission": [
    {
     "paymentTransaction": "velit veniam aliquip nulla cupidatat",
     "commissionTransaction": "laborum fugiat eu in"
    },
    {
     "paymentTransaction": "Excepteur officia proident n",
     "commissionTransaction": "culpa exercitation"
    }
   ],
   "dateCreate": "ex laborum non magna labore",
   "dateConfirm": "et sed",
   "addressContractFrom": "in enim",
   "addressContractTo": "ut non",
   "adressFrom": "laboris",
   "currency": "cupidatat sunt ex Lorem"
  }
 ]
}
```
## Notifications management

### Update notifications

Request type and URL:
```
GET https://zam.io/api/notifications?offset=0&limit=10
```

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {
  "notifications": [
   {
    "title": "Money purchase",
    "text": "2.1 ETH were purchased to Your wallet (address ...), from address ... в 09:02:57 18.06.2021y.",
    "date": "2020-10-05T10:04:07.666Z",
    "read": true
   },
   {
    "title": "Money purchase",
    "text": "2.1 ETH were purchased to Your wallet (address ...), from address ... в 09:02:57 18.06.2021y.",
    "date": "1947-04-26T15:41:07.646Z",
    "read": true
   }
  ],
  "pagination": {
   "offset": 0,
   "limit": 10,
   "total": 100
  }
 }
}
```
## Claculation requests

### Transaction information request
Request to get approximate rate and cost of transaction. Headers are described below.

Request type and URL:
```
GET https://zam.io/api/calculation?priority=MEDIUM&blockchainFrom=doadipisicing&blockchainTo=doadipisicing&addressContractFrom=doadipisicing&addressContractTo=doadipisicing&adressFrom=doadipisicing&addressTo=doadipisicing&amount=doadipisicing&paymentRestrictionTransaction=doadipisicing
```

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {
  "blockchainFrom": "ea",
  "blockchainTo": "labore nostrud",
  "addressTo": "laborum in",
  "amount": "deserunt",
  "commission": [
   {
    "paymentTransaction": "dolor dolore laboris",
    "commissionTransaction": "in adipisicing"
   },
   {
    "paymentTransaction": "amet Excepteur",
    "commissionTransaction": "Duis amet"
   }
  ],
  "dateActual": "enim est quis in",
  "addressContractFrom": "cupidatat sit",
  "addressContractTo": "dolor commodo proident dolore in",
  "adressFrom": "do eu deserunt nostrud",
  "currency": "in"
 }
}
```
