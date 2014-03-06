---
author: Mohamad Hallal
layout: post
title: "Symfony 2.4 Expression Language"
date: 2014-02-21 12:57
comments: true
categories:
- Symfony2
- Expression Language
- SEO
- Content
---

Expression Language is a new awesome component that got released in the latest [Symfony2.4](http://symfony.com/blog/symfony-2-4-0-released). This component allows string expressions
to be parsed, compiled and evaluated in PHP. It was basically extracted from the Twig library that supports writing expression logic in templates.

As simple as:-
```
$expLang = new ExpressionLanguage();
$x       = "1+1";
echo $expressionLang->evaluate($x);
```
The previous code will output "2"!

It seems simple enough, but the benefits that it introduces are great! Especially when we talk about a company like Namshi, where there are dedicated and specialized content and marketing teams with
hundreds of templates and segments of information that must be delivered and shown on the website. The goal is to provide the most flexibility to other departments in creating their information
with the least overhead in terms of code maintenance to the development team.

<!-- more -->
Fabien Potencier, the creator of Symfony, points out that one of the advantages of introducing this component will be the ability to overcome the limitation of the "static configuration"
anywhere in the Symfony2 application such as in services and routing. Imagine that instead of having one route to point to a controller, you can give an expression that sends the request of
the same path to different controllers DYNAMICALLY! More explanation about this is introduced [here](http://symfony.com/blog/new-in-symfony-2-4-the-expressionlanguage-component#routing).

So, let's take a look on this component in some more detail.

The component supports a set of pre-defined operators and functions that can be used to construct complex expressions, we list them and their PHP counterparts:-

    1. binary operators and functions
        - '**' => 'pow'
        - '..' => 'range'
        - 'in' => 'in_array'
        - 'not in' => '!in_array'
        - '&& || | ^ & == === != !== < > >= <= + = * / %' => same in php
        - 'A matches B' => preg_match(A,B)
    2. Unary operators
        - '!' => '!'
        - 'not' => '!'
        - '+' => '+'
        - '-' => '-'
    3. Object Attributes
        - 'foo.one' => 'foo->one'
    4. Arrays
        - '[foo1,foo2]' => array("foo1", "foo2")
    5. Functions
        - 'foo(a,b,c)' => call_user_func_array(array(foo), array(a, b, c)
    6. Names:
        - 'foo' => $foo


The killer feature is, as we are going to see next, is the ability to register your own functions (similar to Twig) in the component to be utilized in expressions.

The component provides public access to methods that allows you to create your own implementations of expression functions.


{% codeblock '' lang:php %}
<?php
$expLang->register('functionNameToBeUseInTheStringExpression', function(){
    //compile callback function
}, function() {
    //evaluateCallbackFunction
});
{% endcodeblock %}

As its name implies, this function registers or adds the required support to call a function within the expression string.
This method has two callback functions, one to be called when the expression is compiled, and the other when its evaluated.

{% codeblock '' lang:php %}
<?php
$expLang->compile('stringExpression');
{% endcodeblock %}

This method, returns the equivalent PHP expression without evaluating it, so you can evaluate it after doing some required manipulation.

{% codeblock '' lang:php %}
<?php
echo($expLang->compile("'test' matches 'test2'"));
//shows preg_match("test", "test2")
{% endcodeblock %}

{% codeblock '' lang:php %}
<?php
$expLang->evaluate('stringExpression', array $values);
{% endcodeblock %}

This function both compiles the expression to PHP and evaluates it to return the outcome. The expression is evaluated for both the built in functions and operators and the evaluate callback function
of a registered custom function.
It also allows to send a set of default predefined ```$values``` to the evaluation process to be passed to the evaluate callback.

{% codeblock '' lang:php %}
<?php
$expLang->parse('stringExpression', array $names);
{% endcodeblock %}

This function parses a string expression from a Token Stream to a Node Tree (the component is built using the Abstract Syntax Tree algorithm) so you can have your expression
as a tree of nodes. Not really sure how it can be benefited from (please share your ideas!).

In a simple ```var_dump()``` I can show you what this method can return

{% codeblock '' lang:php %}
var_dump($expLang->parse('1 + 2', array()));
{% endcodeblock %}

```
object(Symfony\Component\ExpressionLanguage\ParsedExpression)#18 (2) {
  ["nodes":"Symfony\Component\ExpressionLanguage\ParsedExpression":private]=>
  object(Symfony\Component\ExpressionLanguage\Node\BinaryNode)#17 (2) {
    ["nodes"]=>
    array(2) {
      ["left"]=>
      object(Symfony\Component\ExpressionLanguage\Node\ConstantNode)#15 (2) {
        ["nodes"]=>
        array(0) {
        }
        ["attributes"]=>
        array(1) {
          ["value"]=>
          int(1)
        }
      }
      ["right"]=>
      object(Symfony\Component\ExpressionLanguage\Node\ConstantNode)#16 (2) {
        ["nodes"]=>
        array(0) {
        }
        ["attributes"]=>
        array(1) {
          ["value"]=>
          int(2)
        }
      }
    }
    ["attributes"]=>
    array(1) {
      ["operator"]=>
      string(1) "+"
    }
  }
  ["expression":protected]=>
  string(5) "1 + 2"
```

It can perhaps be used to give flexibility for developers to inject and manipulate expressions coming from a non-configurable source.

Here is a comprehensive example of all the previously described methods:

{% codeblock '' lang:php %}
use Symfony\Component\ExpressionLanguage\ExpressionLanguage;

$expLang = new ExpressionLanguage();
$expLang->register('plus', function(), function($args, $a, $b) {
    return $a + $b;
}

$expression = 'plus(1,5)';
echo($expLang->parse($expression)) . PHP_EOL;
echo($expLang->compile($expression)) . PHP_EOL;
echo($expLang->evaluate($expression)) . PHP_EOL;
{% endcodeblock %}

```
plus(1,5)
1 + 5
6
```

Since here at [Namshi](http://namshi.com/) we are using Symfony2 as the main PHP framework for our Backend and API development efforts. We are up-to-date with latest releases
from Symfony. The Expression Language Component allowed us to introduce more flexibility in the way other departments such as Content and Marketing tailor views and serve data
based on some client related information.

We were able to integrate Twig, YAML and the Expression Language Component using Redis as the mega storage unit.

As an example, we have a set of Twig templates for the SEO guys to store the pages' meta information stored in a redis hash and indexed by the path info of the request,
and they are json encoded:

```
"/shoes/" =>
[["locale(en_AE)", {"header":"Buy shoes from Namshi in UAE right on your {{ device }}", "pageTitle":"Welcome to Namshi UAE"}],
["locale(en_SA)", {"header": "Buy shoes from Namshi in Saudi right on your {{ device }}, "pageTitle":"Welcome to Namshi Saudi"}]]
```

And in a ```device``` redis hash, we have the corresponding possible values for ```device``` indexed by the device type

```
"mobile" =>
["device":"Mobile Phone"]
```

Now, we fetch the appropriate template based on the path info and select the first template that its condition evaluates to ```true```, fetch the context (device) based on
client information, render the twig template with the appropriate device value and parse it as YAML!

Was that fast? sorry, here is a sample code:

{% codeblock '' lang:php %}
<?php
$redis      = //the redis instance
$path       = //get the required path from the request
$device     = //get the device type from a service or a header
$expLang    = //the expression language instance
$yamlParser = //the YAML parser instance
$orgLocale  = //get the locale value from a service or a header

$expLang->register('locale', function(), function($values, $locale) {
    if ($locale == $orgLocale) {
        return true;
    }

    return false;
});

$possibleTemplates = json_decode($redis->hget("seo_templates", $path), true);
$contextValues     = json_decode($redis->hget("seo_device", $device), true);

foreach ($possibleTemplates as $possibleTemplate) {
    list($condition, $template) = $possibleTemplate;

    if ($expLang->evaluate($condition)) {
        $twig             = new \Twig_environment(new Twig_Loader_String($template));
        $renderedTemplate = $twig->render(json_encode($template), $contextValues);

        return $yamlParser->parse($renderedTemplate);
    }
}

//Note: this code requires lots of error checking and try, catch blocks to detect and report errors and exceptions since
// content and SEO information, for example, are considered fragile and sensitive
{% endcodeblock %}


Nice, easy and simple. This approach for a CMS like system, provides the following benefits:

* High flexibility in creating information (content, SEO, etc..)
* Tailoring data according to client information (mobile, desktop, large/small screens, app, tablet, etc..)
* Can be based on numerous variables (path information, headers, domain, device type, etc..)
* Content specialization and less dependency on the tech team.
* Less code maintenance overhead since the expected future requirements can only be limited to registering Expression Language functions.
