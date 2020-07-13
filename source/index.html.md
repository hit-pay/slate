---
title: Hitpay Payment Request API

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - php

toc_footers:
  - <a href='https://dashboard.staging.hit-pay.com/'>Sign Up for a Developer Key</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Overview and Setup
HitPay provides a seamless payment experience for your customers 
and an easy integration process for the developers. 
Hitpay payment works by creating Payment Request and then the customers accepting the Payment Request. 

1. Create payment request URLs, each request has a unique Request Id.
2. Your customer using the URL makes the payment with their choice of payment method.
3. You receive the payment confirmation with a unique Payment ID.

## Setup 
1. Create a Hitpay account [here](https://dashboard.staging.hit-pay.com)
2. Setup PayNow (Singapore Only)  in the HitPay Dashboard under Settings > Payment Methods > PayNow
3. Connect Stripe account (to accepts card payments) under Settings > Payment Methods > Credit Cards
4. Generate API keys under Settings > Payment Gateway > API Keys.  These keys will give you access to create payment requests.

# Integration Steps

1. Create a payment request 
2. Redirect the customer to the payment link 
3. Handle payment confirmation/failure using webhooks & redirects
4. Get payment request status 


# Authentication

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "X-BUSINESS-API-KEY: meowmeowmeow"
```

```php


```

> Make sure to replace `meowmeowmeow` with your API key.

Hitpay uses API keys to allow access to the API. You can register a new API key at our [developer portal](https://dashboard.staging.hit-pay.com/).

Hitpay expects for the API key to be included in all API requests to the server in a header that looks like the following:

`X-BUSINESS-API-KEY: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Payment Requests

## Create Payment Request

```shell
curl POST "https://api.staging.hit-pay.com/v1/payment-requests"
  -H "X-BUSINESS-API-KEY: meowmeowmeow"
  -H "Content-Type: application/x-www-form-urlencoded"
  -data-urlencode "email=tom@example.com"
  -data-urlencode "redirect_url=https://example.com/success"
  -data-urlencode "webhook=https://example.com/webhook"
  -data-urlencode "amount=599"
  -data-urlencode "currency=SGD"

```

```php

```

> The above command returns JSON structured like this:

```json
{
    "id": "90f28b43-2cff-4f86-a29e-15697424b3e7",
    "name": null,
    "email": "tom@example.com",
    "phone": null,
    "amount": "599.00",
    "currency": "SGD",
    "status": "pending",
    "purpose": null,
    "reference_number": null,
    "payment_methods": [
        "paynow_online",
        "card",
        "wechat",
        "alipay"
    ],
    "url": "https://securecheckout.staging.hit-pay.com/payment-request/@amazone-prime-pte-ltd/90f28b43-2cff-4f86-a29e-15697424b3e7/checkout",
    "redirect_url": "https://example.com/success",
    "webhook": "https://example.com/webhook",
    "send_sms": true,
    "send_email": true,
    "sms_status": "pending",
    "email_status": "pending",
    "allow_repeated_payments": true,
    "expiry_date": null,
    "created_at": "2020-07-03T02:18:49Z",
    "updated_at": "2020-07-03T02:18:49Z"
}
```

This endpoint creates a new payment request.

### HTTP Request

`POST https://api.staging.hit-pay.com/v1/payment-requests`

### Query Parameters

<aside class="notice">
Mandatory fields are <code>amount</code> and <code>currency</code>. Remember to include header <code> Content-Type: application/x-www-form-urlencoded </code> 
</aside>

Parameter | Description | Example
--------- | ------- | -----------
amount |  Amount related to the payment | 2500.00
payment_methods[] | Choice of payment methods you want to offer the customer | paynow_online , card, wechat, alipay
currency |  Currency related to the payment  | SGD
email | Buyer’s email | foo@example.com
purpose | Purpose of the Payment request  FIFA 16
name |  Buyer’s name |  John Doe
phone | Buyer’s phone number | 9999999999
reference_number | Arbitrary reference number that you can map to your internal reference number. This value cannot be edited by the customer | XXXX123
redirect_url | URL where we redirect the user after a payment. Query arguments payment_request_id and  status are sent along | https://example.com/callback
webhook | URL where our server do POST request after a payment If done | https://example.com/webhook
allow_repeated_payments | If set is true, multiple payments can be paid on a payment request link. Default value is false | false
expiry_date | Time after which the payment link will be expired.Applicable for repeated payments. Default is Null | 2021-02-02 01:01:01



### Response

All the request data included

Parameter | Description
--------- | -------
id | Payment request id 


## Delete Payment request

```shell
curl DELETE "https://api.staging.hit-pay.com/v1/payment-requests/{request_id}"
  -H "X-BUSINESS-API-KEY: meowmeowmeow"
  -H "Content-Type: application/x-www-form-urlencoded"
```

```php

```

> The above command returns JSON structured like this:

```json
{
  "id": "90f28b43-2cff-4f86-a29e-15697424b3e7",
  "deleted" : ":("
}
```

This endpoint deletes a specific payment request.

### HTTP Request

`DELETE https://api.staging.hit-pay.com/v1/payment-requests/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | Payment request ID

## Get Payment Status

```shell
curl GET "https://api.staging.hit-pay.com/v1/payment-requests/{request_id}"
  -H "X-BUSINESS-API-KEY: meowmeowmeow"
  -H "Content-Type: application/x-www-form-urlencoded"
```

```php

```

> The above command returns JSON structured like this:

```json
{
    "id": "90f34585-537f-4cce-8607-629f53c23827",
    "name": null,
    "email": "tom@example.com",
    "phone": null,
    "amount": "599.00",
    "currency": "SGD",
    "status": "completed",
    "purpose": null,
    "reference_number": null,
    "payment_methods": [
        "paynow_online",
        "card",
        "wechat",
        "alipay"
    ],
    "url": "https://securecheckout.staging.hit-pay.com/payment-request/@amazone-prime-pte-ltd/90f34585-537f-4cce-8607-629f53c23827/checkout",
    "redirect_url": "https://example.com/callback",
    "webhook": "https://example.com/webhook",
    "send_sms": true,
    "send_email": true,
    "sms_status": "pending",
    "email_status": "pending",
    "allow_repeated_payments": false,
    "expiry_date": null,
    "created_at": "2020-07-03T10:59:38Z",
    "updated_at": "2020-07-03T11:00:07Z",
    "payments": [
        {
            "id": "90f3459d-1f07-4127-8d68-691b0b239207",
            "quantity": 1,
            "status": "succeeded",
            "buyer_name": null,
            "buyer_phone": null,
            "buyer_email": "tom@example.com",
            "currency": "sgd",
            "amount": "599.00",
            "payment_type": "card",
            "fees": "5.36",
            "created_at": "2020-07-03T10:59:53Z",
            "updated_at": "2020-07-03T11:00:10Z"
        }
    ]
}
```

This endpoint gets the status of a specific payment request.

### HTTP Request

`GET https://api.staging.hit-pay.com/v1/payment-requests/<ID>`

### Request Parameters

Parameter | Description
--------- | -----------
ID | Payment request ID


### Response Parameters

Includes all the data from `Create Payment Request`

Parameter | Description
--------- | -----------
ID | Payment request ID
status | completed/expired/pending/failed. pending if pending payment or the if the id is repeating payment
payments | array of payments made to this request ID. Will contain more than one if its a repeating payment link