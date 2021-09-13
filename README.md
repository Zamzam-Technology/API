<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


# ZAM.IO API PROTOTYPE




  - [Introduction](#introduction)
  - [Additional documents](#additional-documents)
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

# Introduction
A safe wallet to store, manage, send, and receive digital assets. Users need an easy way to access and manage their digital assets. ZamWallet is an application for iOS and Android that allows users to store, access, manage, send, and receive crypto directly from their mobile phones.

# Additional documents
* [API's yaml](https://github.com/Zamzam-Technology/API/blob/main/ZAM.IO_API_PROTOTYPE_yaml.yml)
* [API's swagger](https://github.com/Zamzam-Technology/API/blob/main/ZAM.IO_API_PROTOTYPE_swagger.json)
* [API's postman collection](https://github.com/Zamzam-Technology/API/blob/main/API_Postman_Collection.json)

# General API information
The Zam.io API that serves for creating and managing Your wallet:
* Registration and account management
* Cryptocurrency buy/sell orders
* Working with external wallets
* Ability to update rate information

# HTTP Return Codes

* HTTP `4XX` return codes are used for malformed requests;
  the issue is on the sender's side.
* HTTP `400` return code is used when the WAF Limit (Web Application Firewall) has been violated.
* HTTP `404` return code is used when requested data not found.
* HTTP `408` return code is used when request timeout exceeded.
* HTTP `5XX` return codes are used for internal errors; the issue is on
  Zam's side.
  It is important to **NOT** treat this as a failure operation; the execution status is
  **UNKNOWN** and could have been a success.
* HTTP `500` return code is used when internal server error occured.
* HTTP `503` return code is used when requested service is unreachable.

# Error Codes
* Any endpoint can return an ERROR

Sample Payload below:
```json
{
 "meta": {
  "status": "OK",
  "message": "ERROR - Authorization error"
 },
 "data": {}
}
```

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
      "fullName": "Name Surname",
      "refCode": "{referral code}",
      "codeRequest": 
      {
       "phone": "0647744360"
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
      "passportMessage": "Passport phrase",
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
     "oldPassword": "839a54bf20626e4942bc8f11873f0654",
     "newPassword": "fb29ed3264c5a92bcf74eccd7489e828"
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
  "privateKey": "bd0c8e3bd998a500b0984584a7245ca0"
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
    "token": "94a08da1fecbb6e8b46990538c7b50b2",
    "deviceId": "123e4567-e89b-12d3-a456-426614174000",
    "login": "UserName",
    "password": "fb29ed3264c5a92bcf74eccd7489e828"
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
  "token": "94a08da1fecbb6e8b46990538c7b50b2",
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

Name | Type | Description | Example
------------ | ------------ | ------------ | ------------
priority | STRING | Transaction priority (default value - MEDIUM, optional - TOP). | MEDIUM
blockchainFrom | STRING | Source platform name. | Binance
blockchainTo | STRING | Destination platform name. | Binance
addressContractFrom | STRING | Contract adrress on source wallet. | 
addressContractTo | STRING | Contract address on destiantion wallet. | 
adressFrom | STRING | Soruce wallet address. | cosmos18ta688t40kmped54stvdgvgrazmyh0zqwhmhf5
addressTo | STRING | Destionation wallet address. | 12PUTiN2VjQQzkmSNU7AauHNrQSuadbmhH
currency | STRING | Cryptocurrency shortname. | BTC
amount | STRING | Cryptrocurrency amount. | 3.45
dateCreate | STRING | Order creation date:time. | 1947-04-26T15:41:07.646Z

Request type and URL:
```
GET https://zam.io/api/order?priority=MEDIUM&blockchainFrom=string&blockchainTo=string&addressContractFrom=string&addressContractTo=string&adressFrom=string&addressTo=string&amount=string&paymentRestrictionTransaction=string
```

Response parameters:

Name | Type | Description
------------ | ------------ | ------------
blockchainFrom | STRING | Source platform name
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
  "blockchainFrom": "",
  "blockchainTo": "",
  "addressTo": "",
  "amount": "34.00",
  "dateCreate": "1947-04-26T15:41:07.646Z",
  "addressContractFrom": "",
  "addressContractTo": "",
  "adressFrom": "",
  "currency": "USD"
 }
}
```
### Order for cryptocurrency buy
The buy operation that follows after receiving transaction order.

Request parameters:

Name | Type | Description | Example
------------ | ------------ | ------------ | ------------
priority | STRING | Transaction priority (default value - MEDIUM, optional - TOP). | MEDIUM
blockchainTo | STRING | Destination platform name. | Binance
addressContractTo | STRING | Contract address on destiantion wallet. | 
addressTo | STRING | Destionation wallet address. | 12PUTiN2VjQQzkmSNU7AauHNrQSuadbmhH
currency | STRING | Cryptocurrency shortname. | BTC
amount | STRING | Cryptrocurrency amount. | 3.45
dateCreate | STRING | Order creation date:time. | 1947-04-26T15:41:07.646Z
order | STRING | Reference to transaction order for payment.


Request type and URL:
```
PUT https://zam.io/api/order/purchase?priority=MEDIUM&blockchainTo=string&addressContractTo=string&addressTo=string&amount=string&paymentRestrictionTransaction=string
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
  "blockchainTo": "",
  "addressTo": "",
  "amount": "34.00",
  "dateCreate": "1947-04-26T15:41:07.646Z",
  "order": "",
  "addressContractTo": "",
  "currency": "USD"
 }
}
```
### Order for cryptocurrency sell
The sell operation that follows after receiving transaction order.
FPS - Fast Payment System

Request type and URL:
```
PUT https://zam.io/api/order/sale?priority=MEDIUM&blockchainFrom=string&blockchainTo=string&addressContractFrom=string&addressContractTo=string&adressFrom=string&addressTo=string&amount=string&paymentRestrictionTransaction=string
```

Request parameters:

Name | Type | Description
------------ | ------------ | ------------
spbPhone1 | STRING | Phone number registered on FPS.
spbName1 | STRING | Full name registered on on FPS.
visaCardNumber2 | STRING | 16 digit card number.
visaName2 | STRING | Fullname of card holder.
masterCardNumber3 | STRING | 16 digit card number.
bankAccountPaymentAccount4 | STRING | 
bankAccountBIK4 | STRING | 
bankAccountName4 | STRING | 

Request example:
```json
{
    "spbPhone1": "",
    "spbName1": "",
    "visaCardNumber2": "0000 0000 0000 0000",
    "visaName2": "Name Surname",
    "masterCardNumber3": "0000 0000 0000 0000",
    "masterName3": "Name Surname",
    "bankAccountPaymentAccount4": "",
    "bankAccountBIK4": "",
    "bankAccountName4": ""
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
  "blockchainFrom": "",
  "blockchainTo": "",
  "addressTo": "",
  "amount": "34.00",
  "dateCreate": "1947-04-26T15:41:07.646Z",
  "addressContractFrom": "",
  "addressContractTo": "",
  "adressFrom": "",
  "currency": "USD"
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
 "blockchain": "Zam",
 "addressContract": "",
 "adress": ""
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
DELETE https://zam.io/api/otherCrypto?blockchain=string&addressContract=string&address=string
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
   "blockchain": "BTC",
   "address": "",
   "currency": "BTC",
   "rateToDollar": "100000.00",
   "rateToZam": "0.5",
   "amount": "34.5",
   "dateRate": "1947-04-26T15:41:07.646Z",
   "addressContract": ""
  },
  {
   "blockchain": "ETH",
   "address": "",
   "currency": "BTC",
   "rateToDollar": "200",
   "rateToZam": "0.005",
   "amount": "34.5",
   "dateRate": "1947-04-26T15:41:07.646Z",
   "addressContract": ""
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
GET https://zam.io/api/rate/dollar?currency=string&blockchain=string&addressContract=string
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
 "data":{
  "blockchain": "ETH",
  "address": "",
  "currency": "BTC",
  "rateToDollar": "200",
  "rateToZam": "0.005",
  "amount": "34.5",
  "dateRate": "1947-04-26T15:41:07.646Z",
  "addressContract": ""
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
GET https://zam.io/api/rate/zam?currency=string&blockchain=string&addressContract=do dipisicing
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
 "data":{
  "blockchain": "ETH",
  "address": "",
  "currency": "BTC",
  "rateToDollar": "200",
  "rateToZam": "0.005",
  "amount": "34.5",
  "dateRate": "1947-04-26T15:41:07.646Z",
  "addressContract": ""
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
   "blockchain": "BTC",
   "address": "",
   "currency": "BTC",
   "amount": "34.56",
   "addressContract": ""
  },
  {
   "blockchain": "Zam",
   "address": "",
   "currency": "Zam",
   "amount": "34.56",
   "addressContract": ""
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
   "blockchainFrom": "BTC",
   "blockchainTo": "Zam",
   "addressTo": "",
   "amount": "34.56",
   "commission": [
    {
     "paymentTransaction": "",
     "commissionTransaction": ""
    },
    {
     "paymentTransaction": "",
     "commissionTransaction": ""
    }
   ],
   "dateCreate": "1947-04-26T15:41:07.646Z",
   "dateConfirm": "1947-04-26T15:41:07.646Z",
   "addressContractFrom": "",
   "addressContractTo": "",
   "adressFrom": "",
   "currency": ""
  },
  {
   "blockchainFrom": "ETH",
   "blockchainTo": "Zam",
   "addressTo": "",
   "amount": "34.56",
   "commission": [
    {
     "paymentTransaction": "",
     "commissionTransaction": ""
    },
    {
     "paymentTransaction": "",
     "commissionTransaction": ""
    }
   ],
   "dateCreate": "1947-04-26T15:41:07.646Z",
   "dateConfirm": "1947-04-26T15:41:07.646Z",
   "addressContractFrom": "",
   "addressContractTo": "",
   "adressFrom": "",
   "currency": ""
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
## Calculation requests

### Transaction information request
Request to get approximate rate and cost of transaction. Headers are described below.

Request parameters:

Name | Type | Description | Example
------------ | ------------ | ------------ | ------------
priority | STRING | Transaction priority (default value - MEDIUM, optional - TOP). | MEDIUM
blockchainFrom | STRING | Source platform name. | Binance
blockchainTo | STRING | Destination platform name. | Binance
addressContractFrom | STRING | Contract adrress on source wallet. | 
addressContractTo | STRING | Contract address on destiantion wallet. | 
adressFrom | STRING | Soruce wallet address. | cosmos18ta688t40kmped54stvdgvgrazmyh0zqwhmhf5
addressTo | STRING | Destionation wallet address. | 12PUTiN2VjQQzkmSNU7AauHNrQSuadbmhH
currency | STRING | Cryptocurrency shortname. | BTC
amount | STRING | Cryptrocurrency amount. | 3.45
paymentRestrictionTransaction | STRING | Payment limit for transaction. | 5.147

Request type and URL:
```
GET https://zam.io/api/calculation?priority=MEDIUM&blockchainFrom=string&blockchainTo=string&addressContractFrom=string&addressContractTo=string&adressFrom=string&addressTo=string&amount=string&paymentRestrictionTransaction=string
```

Response example:
```json
{
 "meta": {
  "status": "OK",
  "message": "Success"
 },
 "data": {
  "blockchainFrom": "",
  "blockchainTo": "",
  "addressTo": "",
  "amount": "",
  "commission": [
   {
    "paymentTransaction": "",
    "commissionTransaction": ""
   },
   {
    "paymentTransaction": "",
    "commissionTransaction": ""
   }
  ],
  "dateActual": "1947-04-26T15:41:07.646Z",
  "addressContractFrom": "",
  "addressContractTo": "",
  "adressFrom": "",
  "currency": ""
 }
}
```
