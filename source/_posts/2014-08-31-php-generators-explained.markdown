---
layout: post
title: "PHP Generators explained"
date: 2014-08-31 13:05
comments: true
categories: PHP, generators
author: Hossam Faris
---

`Generators` are one of the new cool stuff on `PHP 5.5`, but it's not a new feature in software programming languages, for example it's on `Python` since version 2.2 on 2001 and now in the draft `ECMAScript` 6  (Javascript) specification.

They are a simple and powerful tool for creating `iterators`. They are like regular functions but using the `yield` statement whenever they want to return data. A `generator` looks like a function but behaves like an iterator, it's useful for `lazy evaluation` "which delays the evaluation of an expression until its value is needed".
<!-- more -->
Here are some examples in different languages:

```js
// infinite id generator in JavaScript

function* generateId(){
    var index = 0;

    while(true) {
        yield index++;
    }
}

var gen = idMaker();
console.log(gen.next().value); // 0
console.log(gen.next().value); // 1
console.log(gen.next().value); // 2
```

```python
//Python

def generateId(n):
    num = 0
    while num < n:
        yield num ++

count_gen_ids = count(generateId(1000))
```

```php
//PHP
<?php

function generateId() {
    $currentId = 1;

    while(true) {
        yield ++ $currentId;
    }
}

for($index = 0; $index < 1000) {
    echo $index;
}

```

In this examples you would notice that we are using the keyword `yield` instead of `return`. `return` will pass value to the function caller then the function will finish the execution, `yield` will pass a value then pause the execution of the function "which now became a generator" till the function caller ask for the next value. Then `yield` again and again will return values to the function caller.

Using generators is useful in cases where you want iterations on demand, and you will end up with less resources consumption and also still the chance to send some data back to the generator function on any given iteration.

When you have situations that you are fetching large sets of data, you can use generators to `lazy load` ("lazy load is to post pone fetching data till the point that you actually need it) your data sets or doing a lot of computation with big amounts of data.

Imagine that you have the code to execute a query on the database, instead of fetching and returning the whole data at the same time, you can simply return a generator function containing the query cursor that yields one data row at a time.


```php
<?php

function getUsers()
{
    $dbh = new PDO('mysql:host=xyz;port=xyz;dbname=xyz', '[...]');
    $stmt = $dbh->prepare("SELECT * FROM users");
    $stmt->execute();

    try {
        while ($rs = $stmt->fetch(PDO::FETCH_OBJ)) {
           yield $rs;
        }
    } finally {
        $stmt->closeCursor();
    }
 }


$users = getUsers();

foreach ($users as $user){
    print_r($user);
}
```

Speaking of Javascript, one great usage example for generators is the [task](http://taskjs.org/) library, a combination of [`promises`](http://domenic.me/2012/10/14/youre-missing-the-point-of-promises/), and `generators` to write asynchronous callbacks as beautiful as synchronous code.

```js
function go() {
    var { spawn, join } = task;

    var out = document.getElementById("out");

    spawn(function() {
        out.innerHTML = "reading...\n";
        try {
            var [ f1, f2 ] = yield join(read("sleep.html"), read("read.html"));
            out.innerHTML += "sleep.html: " + (f1.responseText.length) + "\n";
            out.innerHTML += "read.html: " + (f2.responseText.length) + "\n";
        } catch (e) {
            e.stack.split(/\n/).forEach(function(line) { console.log(line) });
            console.log("");
            out.innerHTML = "error: " + e;
        }
    });
}
```

Finally you need to consider some stuff before start using `Generator`, beware that once it get consumed you cannot reset it, you will need to recall it again.

Also keep resources like db connection and file stream busy on the `Generator` function could be a problem if you are heavily use this resources and you need to release it as soon as possible.