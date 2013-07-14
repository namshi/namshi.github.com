---
author: Ayham Alzoubi
layout: post
title: "Frameworks And Tests in Namshi"
date: 2013-06-16 15:44
comments: true
published: false
categories:
- Phpunit
- Automated Testing
---
Since we are doing tickets and doing coding tasks every day because of our requirements changes
we faced a big problem which was some of our changes is breaking other working parts of owr
applications without we know that its breaking until we deploy live and then we find bugs or
other people tell us that there is a bug in our code because the manual test is not enough and
even if we where careful some times we forget to test all affected parts by our changes and after
deployment we need to test every thing and if some thing was broken we need to fix is ASAP.

<!-- more -->

We were working with [Zend Framework](http://framework.zend.com/) which is good to do your project
as MVC but we started moving to [Symfony2](http://symfony.com/) to make our job easier and our project
more organized and one of the reasons was to do automated test for testing our changes before each
deployment to be sure that we didn't break any thing of other parts and its difficult to add the test
after you finish your application so you need to implement your test with development because you need to
add test  to each part and the best way is adding the test during developing that part.


####Testing our project using phpunit

Testing the project is one of the most important parts in any application and if you want to add test
for all your project you will need to add two types of tests:

- #####Unit Test
Unit Test which is usually against specific classes to test your classes functions input and output
and the changes that functions do

- #####Functional Test
Functional test is used to check the integration between [Namshi](http://namshi.com/) application parts and
layers, this test enable you to test all your application parts and check if it matches the requirement
and always you can be sure that any change in the future doesn't affect your layer or other parts of
your application

####Why are we losing time to implement automated tests
Actually we are not losing time in implementing automated tests because the time we save in development
without test we spend it in solving issues and in every deployment must probably we find issues related
to different functional parts of our application and if we compare between the time to implement the
test and the time we spent in solving issues after each deployment we will find that adding the test
save more time and give us more confidence.
and the most thing we are losing without test is that other people won't trust us anymore so its
better to do automated test than causing bugs.

####How to implement automated test
In symfony2 its not difficult to implement your Tests, you need to install
[phpunit](http://phpunit.de/manual/current/en/index.html) and then you need to implement the test
in your application


####Is the test affect your data or add dummy data
For this point we create new databases for test environment do don't change any thing in our real data
and we are using fixtures to insert dummy data into test databases using
[DoctrineFixturesBundle](http://symfony.com/doc/current/bundles/DoctrineFixturesBundle/index.html)
and we just add required data for the test that we are doing and we clear this data before each test


####Testing IN [Namshi](http://namshi.com/)
We are implementing our test with all new parts of owr applications and our purpose is to cover all
our application with automated test either functional test or unit test, in that case we will be very
far from breaking any part of our application and won't get bugs after deployment

for now we did about 50 test cases
{% img left /images/posts/test-result-success.png test result %}


and if some thing breaking any part of our application for any reason the test won't pass
{% img left /images/posts/test-result-failure.png test result %}

####How we assert test results?
we can assert test results in different ways depending on the response of our request that we are trying
to test so we can assert the response:
```
$this->assertEquals(400, $response->getStatusCode());
$this->assertContains("Expected text", $crawler->filter('*')->text());
```

we can also use [DOMXPath](http://php.net/manual/en/class.domxpath.php) to filter html result
```
$xpath  = new DOMXPath($doc);
$this->assertContains('expected value', $xpath->query("//td[@class='html tag class']")->item(0)->nodeValue);
```

or we can expect exceptions
```
/**
 * @expectedException \Namshi\EntityBundle\Exception\InvalidPaymentMethod
 */
 public function testServiceWithInvalidPaymentMethod()
 {
    $data   = initial data
    $result = $service->execute($data);
 }
```