---
author: Mohamad Hallal
layout: post
title: "Behat, Mink and Parallel Runner, A recipe for automated web testing!"
date: 2013-06-02 17:13
comments: true
published: false
categories:
- Behavior Driven Development
- Behat
- Mink
- Parallel Runner
- Automated Testing
---

It was not until I did my first weekend manual testing procedure on Namshi till I came to realize how much time consuming
testing can become. All critical paths starting from registration and login to buying a product using the
available payment methods should be accounted for. Along with that, any irregularities on different pages on the website
must be reported.
<!-- more -->
As Namshi grows and gets more and more complicated, an *Automated Web Testing Tool* had to be put in place as soon 
as possible. An *Automated Web Testing Tool* is aimed to test that different user interactions with the webpage and web application are going
smoothly without errors or unexpected results.

Keeping those requirements in mind, five main components were considered to build the application:
[Symfony2 Framework](http://symfony.com/), [Behat](http://behat.org/) and its
[Mink](http://mink.behat.org/) and [Parallel Runner](https://github.com/shvetsgroup/ParallelRunner)
extensions and [Doctrine Object Relational Mapper (ORM)](http://www.doctrine-project.org/projects/orm.html).

{% img center /images/arch.jpg 'App Architecture' 'App Architecture' %}

##Behat and Gherkin

Behat is a PHP Based Behavior Driven Development (BDD) platform that allows the contribution
of stake holders and customers in the development of software products. In BDD, tests are usually
written before the actual development of a feature actually starts. In my opinion,it is a very powerful
approach to overcome any misunderstandings that may arise between developers and other stake
holders during project planning.

The actual tests are written in the Feature files of Behat using the Gherkin language. The following
is an example of a scenario in the integrity feature of Namshi:-

{% img left /images/gherkin.jpg 'Gherkin' 'Gherkin' %}

As can be noticed, Gherkin is non-technical descriptive language for non-programmers to declare the desired
functionality of the software.

Of course, it is not the case that Behat will automatically understand and run the Feature Files on its own! Behat asks the
programmer to write the definitions of the written statements in a special file named FeatureContext.php right when
you try to run the tests!

{% codeblock FeatureContext.php lang:php %}
<?php
/**
* @Then /^I retrieve a couple product links$/
*/
public function iRetrieveACoupleProductLinks() {
        
    $links = array_slice($this->getPage()->findAll('css','.productsCatalog .itm-link'), 0, 2);
    $productsToVisit = array();

    foreach ($links as $link) {
        $productsToVisit[] = $link->getAttribute('href');
    }

    $this->linksToVisit = $productsToVisit;
}
{% endcodeblock %}

##Mink

Behat can be used to write tests for any application. In order to test web based applications, Behat has to be extended
with the Mink extension. Mink provides an interface between the web browser and Behat in order to test and run different scenarios.
When extended in the FeatureContext.php, Mink provides a rich library to simulate the interactions that
usually take place between a user on a web browser and the web application.
Mink supports a number of browser emulators: Goutte, Sahi, Zombie, Selenium and Selenium2. In our tool, we are mainly
using Selenium2 driver except for some scenarios where Goutte is used.

To cover Namshi's sets of predefined manual tests, six feature files were created:-

1. integrity.feature: Tests some simple yet critical components of Namshi.
2. news.feature: Covers Scenarios for user subscription to the newsletter.
3. registration.feature: Covers different user registration scenarios.
4. cod.feature (cash on delivery): Scenarios where a user buys a product using cash on delivery.
5. cc.feature (credit card): Scenarios when a user buys a product using credit card payment method.
6. pp.feature (paypal): Scenarios when a user buys a product using paypal payment method.


##Behat Parallel Runner

After some trials with the testing tool, we noticed that execution takes some time to complete (~25 minutes). In order to speed up the
execution, a parallelization tool had to be put in place. **Gearman** is a reputable *Job Server* mostly used to do work in parallel and
in load balancing across processors. It has an extension in Behat that fetches the appropriate Feature Tags to
their corresponding worker processes. The issue with Gearman is that it is very complicated for a simple tool
like the one we are building. Gearman requires a Job Server, OS libraries, PHP extension and Behat extension to operate.
After some research, another tool was found that can deliver the required performance with much less complexity.
**Behat Parallel Runner** also distributes the BDD Test Suite defined in Behat to a number of Behat Instances
without the need for Gearman's client/worker architecture. This distribution is done by defining different *profiles*
in *behat.yml* configuration file and specify the number of parallel processes to run. Parallel Runner
Extension for Behat was downloaded and enabled through composer and in the behat configuration file.
{% codeblock composer.json lang:json %}
"require": {
    "shvetsgroup/parallelrunner": "dev-master"
}
{% endcodeblock %}

In order to integrate Parallel Worker, a configuration file for each website was created. In each configuration
file, the same set of profiles was defined. We took advantage of Behat's filtering option to enable each profile
to run a certain *tag* that is already set in the *feature files*.
In each configuration file, six profiles were added such that each one runs one *feature tag*.
{% codeblock behat.yml lang:yml %}
F1:
    filters:
        tags: "@int"
F2:
    filters:
        tags: "@news"
F3:
    filters:
        tags: "@reg"
F4:
    filters:
        tags: "@cod"
F5:
    filters:
        tags: "@cc"
F6:
    filters:
        tags: "@pp"
{% endcodeblock %}
Finally, all feature files had to be tagged right before the Feature declaration such that
the extension can identify the scenarios and their corresponding instances.

{% codeblock behat.yml lang:yml %}
default:
    extensions:
        shvetsgroup\ParallelRunner\Extension:
            process_count:  6
{% endcodeblock %}
{% codeblock registration.feature lang:gherkin %}
@reg
Feature: Check Namshi registration Scenarios

    @javascript
    Scenario: As a user I register a new account, filling the registration form with proper data
        Given I visit the "register" page
        When I type in the following:
        |RegistrationForm_email           |registration.new+test-(timeStamp)@namshi.com |
        |RegistrationForm_first_name   |test                                                                        |
        |RegistrationForm_last_name   |test                                                                        |
        |birthday_day                            |12                                                                          |
        |birthday_month                        |05                                                                          |
        |birthday_year                           |1987                                                                      |
        |RegistrationForm_password    |121212                                                                  |
        |RegistrationForm_password2  |121212                                                                  |
        |RegistrationForm_gender        |male                                                                      |
        When I press "send"
        Then I am registered as "test"
{% endcodeblock %}

##Namshi Automated Testing in Action!

Once all settings are completed, we can start the Automated Testing procedure by running Behat
with the *config* parameter set to the target configuration file, which is the site to be tested.
{% codeblock lang:bash %}
bin/behat --config = behat-ae.yml
{% endcodeblock %}
Now, six browser windows will pop up and the test suits get executed in parallel!

{% img left /images/execution.jpg 'Running Test Suite' 'Running Test Suite' %}

When the execution completes, we can open the tool's webpage and review the results!

{% img left /images/results.jpg 'Results on the webpage' 'Results on the webpage' %}

Looks like everything is running smoothly!
