---
layout: post
title: "Innovate Payment Gateway Library"
date: 2014-09-02 19:23
comments: true
categories: [API, payment, gateway, php]
author: Ayham Alzoubi
---

In the tech [Namshi](https://namshi.com) team, as you probably know, we fully support the open source movement,
that's why we try to release open source software as soon as we can, in the full "give back to the community" mindset.
You can check all our project on [github](http://github.com/namshi)

Lots of e-commerce companies and service providers have online payments for their
customers. So do we.

Our weapon of choice, apart from PayPal, to carry out payments is: [Innovate.](http://www.innovatepayments.com)
That's why we developed and released the [Innovate library](https://github.com/namshi/innovate), written in PHP.

[Innovate](http://www.innovatepayments.com) payment gateway accepts all types of credit card payments.

<!-- more -->

## Installation
You can easily integrate the Innovate library in your project via [composer](https://getcomposer.org):
```
composer install namshi/innovate
```

{% img center /images/posts/cards.png payment cards %}


## Usage
Using this library is easy, here is what you need:

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

$client      = new Client($storeId, $authenticationKey); // retrieve them from your Innovate account
$transaction = new Transaction('sale', 'ecom', true, 'ORDER_NUMBER', 'DESCRIPTION', 'USD', 40, 'AN OPTIONAL REFERENCE TO YOUR TRANSACTION');
$card        = new Card('1234123412341234', '111', new \DateTime($cardExpiryDate));
$customer    = new Customer('Mr', 'John', 'Doe');
$address     = new Address('My address info 1', 'My address info 2', 'My address info 3', 'San Francisco', 'California', 'US', '00000');
$billing     = new BillingInformation($customer, $address, "customers's-email@gmail.com", $customerIpAddress);
$browser     = new Browser($customUserAgent, $requestAcceptHeader);
```

We used OOP to cover all the data we need to send to Innovate:

`$client` is a model that contains the Innovate account info.

`$transaction` represents a detailed transaction where we pass as many arguments as Innovate needs.

`$card` represents the required card details to perform the payment.

`$customer` represents the customer details.

`$address` represents the customer address details.

`$billing` represents the customer billing address and contact details.

`$browser` represents detailed browser info, including the User-Agent.

`$customUserAgent` is the browser user agent header.

`$requestAcceptHeader` is the browser accept header.


## How It Works

To perform the payment you need to create a request to [Innovate](http://www.innovatepayments.com) and pass the previous created objects:

```php

<?php

$response = $client->performPayment($transaction, $card, $billing, $browser);
```

You will then get one of the following responses:

* ## Normal transaction
A normal transaction follows a very simple flow: It just requires authentication through Innovate, and the library then performs the payment.

* ## 3D secure transactions
A [3-D_Secure](http://en.wikipedia.org/wiki/3-D_Secure) transaction requires two requests to Innovate and one request for 3D Secure in order
to add an additional security layer while performing the payment.
The first request is already sent to Innovate and if it is 3D Secure transaction you will need to redirect the user to the concerned party
(primarily, the bank) to confirm the payment.

After that the process continues by sending another request to Innovate containing the extra validation values.

You can easily distinguish between the two possible types of payments, 3D Secure and Normal, by looking at the type of Innovate response received:


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

In case of normal Transactions, the library will perform the payment directly.
In case of 3D Secure transactions you need to send another request
with more details (related to the third security party) as follows:

```php
<?php

use Namshi\Innovate\Http\Response\Redirect;

if ($response instanceof Redirect) {
    // build a form
}
```

The response object contains the values ('targetUrl', 'session', 'paReq') which are needed to build the form:

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

`termUrl`: the URL that we need to direct the user to, after the user submits the form.

See an example of an ACS (Access Control Server) Form

We need to submit the form with Javascript:

```javascript

function autosub() {
    document.forms['acsform'].submit();
}

document.onload=autosub;
```

After the form is submitted, the user is redirected to a 3D Secure page which asks for extra credentials.
Once the user submits the 3D Secure form, he/she gets redirected to the 'termUrl' page with two hidden values
('PaRes', 'MD') which are used to create the final request to Innovate:

```php
<?php

$extraData = array(
    'PaRes'     => 'Authentication response', // we get this value from hidden fields after redirection to termUrl
    'session'   => 'MD', // we get this value from hidden fields after redirection to termUrl
);

$finalResponse = $client->send($client->createRemoteRequest('POST', Client::INNOVATE_URL, null, null, $extraData));
```

Then we get the final response which can be checked for payment status - success or denied:

```php
<?php

if (200 === $finalResponse->getStatusCode()) {
    // payment done
} else {
    // authentication failed
}
```

## Tests

We added two types of tests; to run them you need to install the dev dependencies using composer and then run
the test using [PHPUnit](http://phpunit.de)

* ### Functional Test:
These test the library functionality without sending any real request to Innovate, just hit this on your terminal:

```
phpunit
```

* ### integration Test
There are a couple of integration tests that verify that the library works flawlessly by creating
real requests to the Innovate payment gateway. Just use valid test credentials, then create a file
called .innovate.config in the project directory with four parameters:

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

We hope that you enjoy using Namshi's open source library. We've been using this library for almost a year and
it's working fine with no issues on our side. As always, any feedback and pull requests are welcome :)
