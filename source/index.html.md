---
title: Hitpay Payment Request API

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - php

toc_footers:
  - <a href='https://dashboard.sandbox.hit-pay.com/'>Sign Up for a Developer Key</a>
  - <a href='https://dashboard.hit-pay.com/'>Sign Up for a Production Key</a>
  - <a href='https://chat.whatsapp.com/D40dZAQ9Hu02JDBI2ySnck'>Join the developer WhatsApp group </a>

includes:
  - errors

search: true

code_clipboard: true
---

# PayNow APIs and Payment Request APIs
HitPay provides a seamless payment experience for your customers and an easy integration process for the developers.
<br />
<br />
With Hitpay's API integration you can start accepting multiple payment methods including **PayNow (Singapore), Visa, Master, GrabPay, Apple Pay, Google Pay, American Express, AliPay, and WeChat Pay** on a single checkout page hosted on hitpay. 
<br />
<br />
HitPay's PayNow integrations allow you to fully automate PayNow acceptance in Singapore with instant feedback and webhook.

![PayNow API](/images/mock.png)


1. Create payment request URLs, each request has a unique Request Id.
2. Your customer using the URL makes the payment with their choice of payment method.
3. You receive the payment confirmation with a unique Payment ID.

## Setup 
1. Create a Hitpay account [https://dashboard.sandbox.hit-pay.com](https://dashboard.sandbox.hit-pay.com)
2. Setup PayNow (Singapore Only)  in the HitPay Dashboard under Settings > Payment Methods > PayNow
3. Connect Stripe account (to accepts card payments) under Settings > Payment Methods > Credit Cards
4. Generate API keys under Settings > Payment Gateway > API Keys.  These keys will give you access to create payment requests.

### Note:
You can test all our API endpoints in our Sandbox Platform. By signing up on our Sandbox account, you can mock transactions to do end-to-end testing of your Integration flow.

The base URL for production environment will be <code>https://api.hit-pay.com/v1/</code>

The base URL for Sandbox environment will be <code>https://api.sandbox.hit-pay.com/v1/</code>
<aside class="notice">
You can test PayNow transactions on our sandbox by scanning the dummy PayNow QR using any QR code reader. 
</aside>

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
  -H "X-Requested-With: XMLHttpRequest"
```

```php
$request->setHeaders(array(
  'X-BUSINESS-API-KEY' => 'meowmeowmeow',
  'Content-Type' => 'application/x-www-form-urlencoded',
  'X-Requested-With' => 'XMLHttpRequest'
));
```

> Make sure to replace `meowmeowmeow` with your API key.

Hitpay uses API keys to allow access to the API. You can register a new API key at our [developer portal](https://dashboard.sandbox.hit-pay.com/).

Hitpay expects for the API key to be included in all API requests to the server in a header that looks like the following:

`X-BUSINESS-API-KEY: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>
<aside class="warning">
API keys should be kept confidential and only stored on your servers. Do not store it on your mobile or web client. 
</aside>

# Payment Requests

## Create Payment Request

```shell
curl POST "https://api.sandbox.hit-pay.com/v1/payment-requests"
  -H "X-BUSINESS-API-KEY: meowmeowmeow"
  -H "X-Requested-With: XMLHttpRequest"
  -H "Content-Type: application/x-www-form-urlencoded"
  -data-urlencode "email=tom@example.com"
  -data-urlencode "redirect_url=https://example.com/success"
  -data-urlencode "webhook=https://example.com/webhook"
  -data-urlencode "amount=599"
  -data-urlencode "currency=SGD"

```

```php
$client = new http\Client;
$request = new http\Client\Request;
$request->setRequestUrl('https://api.sandbox.hit-pay.com/v1/payment-requests');
$request->setRequestMethod('POST');
$body = new http\Message\Body;
$body->append(new http\QueryString(array(
  'email' => 'tom@example.com',
  'redirect_url' => 'https://example.com/success',
  'webhook' => 'https://example.com/webhook',
  'currency' => 'SGD',
  'amount' => '599')));$request->setBody($body);
$request->setOptions(array());
$request->setHeaders(array(
  'X-BUSINESS-API-KEY' => 'meowmeowmeow',
  'Content-Type' => 'application/x-www-form-urlencoded',
  'X-Requested-With' => 'XMLHttpRequest'
));
$client->enqueue($request)->send();
$response = $client->getResponse();
echo $response->getBody();
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
    "url": "https://securecheckout.sandbox.hit-pay.com/payment-request/@amazone-prime-pte-ltd/90f28b43-2cff-4f86-a29e-15697424b3e7/checkout",
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

This endpoint creates a new payment request. This is the first step of the payment flow, once you have all the details from the user and ready to collect payment use this API to create a payment request.

### HTTP Request

`POST https://api.sandbox.hit-pay.com/v1/payment-requests`

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
reference_number | Arbitrary reference number that you can map to your internal reference number. This value cannot be edited by the customer | XXXX123
redirect_url | URL where we redirect the user after a payment. Query arguments `reference` (payment request id) and  `status` are sent along | https://example.com/callback
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
curl DELETE "https://api.sandbox.hit-pay.com/v1/payment-requests/{request_id}"
  -H "X-BUSINESS-API-KEY: meowmeowmeow"
  -H "X-Requested-With: XMLHttpRequest"
  -H "Content-Type: application/x-www-form-urlencoded"
```

```php

```

> The above command returns JSON structured like this:

```json
{
    "success": true
}
```

This endpoint deletes a specific payment request.

### HTTP Request

`DELETE https://api.sandbox.hit-pay.com/v1/payment-requests/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | Payment request ID

## Get Payment Status

```shell
curl GET "https://api.sandbox.hit-pay.com/v1/payment-requests/{request_id}"
  -H "X-BUSINESS-API-KEY: meowmeowmeow"
  -H "X-Requested-With: XMLHttpRequest"
  -H "Content-Type: application/x-www-form-urlencoded"
```

```php
$client = new http\Client;
$request = new http\Client\Request;
$request->setRequestUrl('https://api.sandbox.hit-pay.com/v1/payment-requests/{request_id}');
$request->setRequestMethod('GET');
$request->setOptions(array());
$request->setHeaders(array(
  'X-BUSINESS-API-KEY' => 'meowmeowmeow',
  'X-Requested-With' => 'XMLHttpRequest'
));
$client->enqueue($request)->send();
$response = $client->getResponse();
echo $response->getBody();
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
    "url": "https://securecheckout.sandbox.hit-pay.com/payment-request/@amazone-prime-pte-ltd/90f34585-537f-4cce-8607-629f53c23827/checkout",
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

`GET https://api.sandbox.hit-pay.com/v1/payment-requests/<ID>`

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



# Webhook
```shell
```

```php
    public function generateSignatureArray($secret, array $args) 
    {   
        $hmacSource = [];        

        foreach ($args as $key => $val) {
            $hmacSource[$key] = "{$key}{$val}";
        }    

        ksort($hmacSource);

        $sig            = implode("", array_values($hmacSource));
        $calculatedHmac = hash_hmac('sha256', $sig, $secret); 

        return $calculatedHmac;
    }
```
> Sample webhook payload data:

```url
payment_id=92965a2d-ece3-4ace-1245-494050c9a3c1&payment_request_id=92965a20-dae5-4d89-a452-5fdfa382dbe1&phone=&amount=599.00&currency=SGD&status=completed&hmac=330c34a6a8fb9ddb75833620dedb94bf4d4c2e51399d346cbc2b08c381a1399c
``` 
Webhook is a POST request send from HitPay's server to your server about the payment confirmation. If you are using hitpay APIs to integrate into your e-commerce checkout you must mark your order as paid ONLY after the webhook is received and validated.

1. Create an endpoint (E.g. /payment-confirmation/webhook) in your server that accepts POST requests. This request is <code>application/x-www-form-urlencoded</code>.
2. Validate the webhook data using your salt value 
3. Return HTTP status code 200 to Hitpay 
4. Mark your order as paid

### Webhook fields
Following fields are sent with the webhook request:

Parameter | Description
--------- | -----------
payment_id |	Payment ID
payment_request_id |	Payment request ID
phone |	Buyer’s phone number
amount |  Amount related to the payment
currency |  Currency related to the payment
status |	Payment status (completed / failed)
reference_number | Arbitrary reference number that you have mapped during payment request creation
hmac |	Message Authentication code of this webhook request

### Validate Webhook

Hitpay creates a list of all values from the key-value pairs that we send in the POST request and sort them in the order of their keys alphabetically. We then concatenate all these values together. We then use the HMAC-SHA1 algorithm to generate the signature. The HMAC key for the signature generation is the secret `salt` from your dashboard under `Settings > Payment Gateway > API Keys`.

Hitpay PHP API Wrapper:
[https://github.com/hit-pay/php-sdk](https://github.com/hit-pay/php-sdk)

# Platform APIs

## Overview

Hitpay Platform APIs are an extension of the above Payment Request APIs are used by e-commerce platforms or aggregators who host other merchants. Platforms will have the ability to create payment requests on behalf of the merchants that are on their platform. 

Platforms will also have the ability to set a commission for the transactions that happen through their platform.

## Integrations Flow
1. Contact to Hitpay to signup for a Platform Account 
2. Individual merchant creates a Hitpay account https://dashboard.sandbox.hit-pay.com 
3. Individual merchant will setup PayNow (Singapore Only) in the HitPay Dashboard under Settings > Payment Methods > PayNow 
4. Individual merchant will generate API keys under Settings > Payment Gateway > API Keys. (Merchant API keys)
5. These keys will give you access to create payment requests. 

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "X-BUSINESS-API-KEY: meowmeowmeow"
  -H "X-PLATFORM-KEY: bowbowbow"
  -H "X-Requested-With: XMLHttpRequest"
```

```php
$request->setHeaders(array(
  'X-BUSINESS-API-KEY' => 'meowmeowmeow',
  'X-PLATFORM-KEY' => 'bowbowbow',
  'Content-Type' => 'application/x-www-form-urlencoded',
  'X-Requested-With' => 'XMLHttpRequest'
));

```

> Make sure to replace `meowmeowmeow` with your Merchant API key and `bowbowbow` with your Platform API key.

## Platform Authentication
Hitpay uses API keys to allow access to the API. Once your account is converted to “Platform Account” you can you can access your [“Platform API Key”](https://dashboard.sandbox.hit-pay.com)  (Settings > Payment Gateway > Platform).
This Platform API Key along with Merchant API key is expected to be included in all API requests to the server in a header that looks like the following:


`X-BUSINESS-API-KEY: meowmeowmeow`
`X-PLATFORM-KEY: bowbowbow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your Merchant API key & <code>bowbowbow</code> with your Platform API key
</aside>