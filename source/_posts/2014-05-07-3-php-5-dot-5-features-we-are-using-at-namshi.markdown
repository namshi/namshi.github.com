---
author: Geshan Manandhar
layout: post
title: "3 PHP 5.5 features we are using at Namshi"
date: 2014-05-07 09:03
comments: true
categories:
- php

---

Here at  [Namshi](http://www.namshi.com) we are constanty updating our software stack to exploit the best benefits latest technology has to offer. Keeping up to date with the latest stable version of PHP, we are using [PHP 5.5](http://php.net/releases/5_5_0.php) for some time now. We also leverage the new features added in PHP 5.5; this post will shed light on some of the features that have come in handy during our development process.

PHP [5.4](https://php.net/manual/en/migration54.new-features.php) and PHP [5.5](https://php.net/manual/en/migration55.new-features.php) both were feature packed PHP releases, we have found 3 of these features useful in our routine PHP development which are discussed below:

<!-- more -->

### array_column function

The short array syntax was introduced in PHP 5.4, which makes creating arrays simpler than the old syntax.

```php
$productIds = [1001, 1005, 1007];

//array with keys and values
$products = [
    ["title" => "Vito Solo Shirt", "sku" => "VI714AT94SIB", "price" => 180.00],
    ["title" => "Core Ultra Shirt", "sku" => "JA872AT40VYL" , "price" => 195.00],
    ["title" => "Swoosh T-shirt", "sku" => "NI727AT17RGG" , "price" => 130.00]
];

```
For arrays like in the above example or for result sets from a database query, the [array_column](http://www.php.net/manual/en/function.array-column.php) PHP function comes in very handy to extract out the needed data as shown below:

```php
$productTitles = array_column($products, "title");

//product titles will be a new array of titles extacted from products array in above example code
Array
(
    [0] => Vito Solo Shirt
    [1] => Core Ultra Shirt
    [2] => Swoosh T-shirt
)

//if you want to create a key value array where key is the sku and value is the price, it can be done by the following call

$productPrices = array_column($products, "price", "sku");

Array
(
    [VI714AT94SIB] => 180
    [JA872AT40VYL] => 195
    [NI727AT17RGG] => 130
)

```
The PHP `array_colum` function is an easy yet effective way to extract out needed informaton from an array which will make working with PHP arrays pleasant than usual. 

### finally

Finally, the `finally` keyword has been added to PHP, as mentioned in the [exceptions](http://www.php.net/manual/en/language.exceptions.php) documentation on the PHP website: 
>Code within the finally block will always be executed after the try and catch blocks, regardless of whether an exception has been thrown, and before normal execution resumes.

This makes a very good use case when some code needs to be executed if an exception occurs and `return` has to be called. A typical example is closing the database connection:

```php
try {
     //open sql connection 
     if ($condition) { 
        return false;
     }
  }
  finally {
    //close the sql connection, this will be executed even if the return is called.
  }
```

There are other use cases of finally, still beware of using it as you might find out your exception has been [silenced](http://www.php.net/manual/en/language.exceptions.php#114207). Use finally with care and utilize it where needed.

### DateTimeImmutable

The [DateTimeImmutable](http://www.php.net/manual/en/class.datetimeimmutable.php) class is useful if you want the date time to stay the same for the execution context. The main instance is never modified but returns a new object instead for any operation like adding date. It works very well when you want to save the `created_at` and `updated_at` date time columns in the database.

An example to show that even adding date to `DateTimeImmutable` class does not change it:

```php
$date           = new DateTimeImmutable();
$datePlusOneDay = $date->add(new DateInterval('P1D'));

print $date->format('Y-M-d H:i:s');
print "---";
print $datePlusOneDay->format('Y-M-d H:i:s');

//output
//2014-May-07 22:37:04---2014-May-08 22:37:04
```

Another example for the `created_at` and `updated_at` use case:

```php
//in some entity class in the setCreatedAt method
$this->createdAt = new DateTimeImmutable();

//in some entity class in some prePresist method
$this->updatedAt = new DateTimeImmutable();
```

There are lots of resources mentioning the new features of PHP 5.4 and PHP 5.5, I hope mentioning this useful subset helps you utilize them to solve issues.
