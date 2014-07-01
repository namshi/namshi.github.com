---
layout: post
title: "Innovate Payment Gateway Library"
date: 2014-06-23 19:23
comments: true
categories: [API, payment, gateway, php]
author: Ayham Alzoubi
---

In the tech [Namshi](https://namshi.com) team, as you probably know, we fully support the open source movement, that's why we try to release open source software as soon as we can, in the full "give back to the community" mindset.
You can check all our project on [github](http://github.com/namshi)

Lots of online services and e-commerce companies have online payments for their
customers, no less is Namshi: we are using [Innovate](http://www.innovatepayments.com), other than PayPal, as payment gateway to perform payments on our apps.
That's why we developed and released the [Innovate library](https://github.com/namshi/innovate) written in PHP.

[Innovate](http://www.innovatepayments.com) payment gateway accepts all types of credit card payments.

<!-- more -->

## Installation
You can easily integrate the Innovate library in your project it via [composer](https://getcomposer.org):
```
composer install namshi/innovate
```

{% img center /images/posts/cards.png payment cards %}


## Usage
Using this library is easy, what you need is:

 * initialize the Innovate client

```php
<?php

use Namshi\Innovate\Payment\Transaction;
use Namshi\Innovate\Payment\BillingInformation;
use Namshi\Innovate\Payment\Browser;
use Namshi\Innovate\Payment\Billing\Customer;
use Namshi\Innovate\Payment\Billing\Address;
use Namshi\Innovate\Payment\Card;
use Namshi\Innovate\Client;

$client      = new Client($storeId, $authenticationKey); // retrieve them from your innovate account
$transaction = new Transaction('sale', 'ecom', true, 'ORDER_NUMBER', 'DESCRIPTION', 'USD', 40, 'AN OPTIONAL REFERENCE TO YOUR TRANSACTION');
$card        = new Card('1234123412341234', '111', new \DateTime($cardExpiryDate));
$customer    = new Customer('Mr', 'John', 'Doe');
$address     = new Address('My address info 1', 'My address info 2', 'My address info 3', 'San Francisco', 'California', 'US', '00000');
$billing     = new BillingInformation($customer, $address, "customers's-email@gmail.com", $customerIpAddress);
$browser     = new Browser($customerUserAgent, $requestAcceptHeader);
```

We used OOP to cover all data we need to send to Innovate:

`$client` is a model that contains the Innovate account info.

`$transaction` represents a transaction as detailed as Innovate needs it and we can pass the needed arguments.

`$card` represents the required card details to perform the payment.

`$customer` represents the customer details.

`$address` represents the customer address details.

`$billing` represents the customer details, the billing address and contact details.

`$browser` represents the browser as detailed, mainly the User-Agent.


## How It Works

To perform the payment you need to create a request to [Innovate](http://www.innovatepayments.com) and pass the previous created objects:

```php

<?php

$response = $client->performPayment($transaction, $card, $billing, $browser);
```

You will then get a response which will be one of these types:

* ## Normal transactions
A normal transaction follows a very simple flow, as it just requires authentication through Innovate, and the library will perform the payment

* ## 3D secure transactions
A [3-D_Secure](http://en.wikipedia.org/wiki/3-D_Secure) transaction requires two requests to innovate and one request for 3-D_Secure as additional security layer to perform the payment.
The first request is already sent to innovate and if it is 3d secure transaction you will need to redirect the user to the 3d security party to
confirm the payment.
After that the process continues by sending a nother request to innovate containing the extra validation values.

You can easily distinguish between the two possible types of payment transactions, 3-D_Secure and the normal transaction, looking at the type of the Innovate response:


```php
<?php

use use Namshi\Innovate\Http\Response\Redirect;

$response = $client->performPayment($transaction, $card, $billing, $browser);

if ($response instanceOf Redirect) {
    // 3D secure transactions
} else {
    // Normal transactions
}
```

In case of normal Transaction the library will perform the payment directly,
but in case of 3D-Secured transaction you need to send another request
with more details related the third security party as the following:

```php
<?php

use Namshi\Innovate\Http\Response\Redirect;

if ($response instanceof Redirect) {
    // build a form
}
```

The response object contains the values ('targetUrl', 'session', 'paReq') which is needed to build the form:

```php
<?php

$targetUrl  = $response->getTargetUrl();
$session    = $response->getSession();
$pareq      = $response->getPareq();
```
Then, the receiving the previous values will be used to build a form as hidden values.
The form will be sent to the `$targetUrl` which is the 3D-secure service page as the following:

```xml

<form name="acsform" action="[targetUrl from the response]" method="post">
    <input type="hidden" name="PaReq" value="[The pareq data from response]">
    <input type="hidden" name="MD" value="[Any value can be stored here which will be sent back unchanged in the the 3d secure response e.g to preserve the session id which we can send back to complete the transaction]">
    <input type="hidden" name="TermUrl" value="[return URL on your site]">
    <noscript>
        <input type="Submit">
    </noscript>
</form>
```

`termUrl`: the url that we need to direct the user to it after he submit the form.

See an example ACS (Access Control Server) Form

We need to submit the form with Javascript:

```javascript

function autosub() {
    document.forms['acsform'].submit();
}

document.onload=autosub;
```

Then after the form is submitted the user will be redirected to 3D-Secure page which asks for extra credintials, then the user submit the 3-D_secure form and gets redirected to 'termUrl' page with two hidden values ('PaRes', 'MD') which are used to create the final request to innovate:

```php
<?php

$extraData = array(
    'PaRes'     => 'Authentication response', // we get this value from hidden fields after redirection to termUrl
    'session'   => 'MD', // we get this value from hidden fields after redirection to termUrl
);

$finalResponse = $client->send($client->createRemoteRequest('POST', Client::INNOVATE_URL, null, null, $extraData));
```

Now we got the final response and we can check if the payment is performed or is denied:
```php
<?php

if (200 === $finalResponse->getStatusCode()) {
    // payment done
} else {
    // authentication failed
}
```

## Tests

We added two types of tests, to run them you need to install the dev dependencies using composer and then run the test using [PHPUnit](http://phpunit.de)

* ### Functional Test:
They test the library functionality without sending any real request to Innovate, just hit this on your terminal:

```
phpunit
```

* ### integration Test
There are a couple of integration tests that verify that the library works flawless creating real requests to the Innovate payment gateway. Just use valid test credentials for that, then create a file called .innovate.config in the project directory with four parameters:

```php

<?php

$configs = array(
    'storeId'           => 'xxxxx', //store Id in Innovate
    'authenticationKey' => 'xxxxxxxxxxx', //authentication key
);

// Card info
$cardInfo = array(
    'number'    => 'xxxxxxxxxxxx',
    'cvv'       => 'XXX',
);

// The card which need redirection for 3d secured
$redirectUrlCardInfo = array(
    'number'    => 'xxxxxxxxxxxx',
    'cvv'       => 'XXX',
);

// your ip, it should be in Innovate white list
$ip = 'xxx.xxx.xxx.xxx';
```

After the setup, just run:

```
phpunit tests/Namshi/Innovate/Test/Integration/ServiceTest.php
```

We hope that you will enjoy using opensource Namshi's library: we've been using this library for almost one year and it's working fine with no issues on our side. Any feedback, pull requests are always welcomed :)
