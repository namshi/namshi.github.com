---
layout: post
title: "Welcome ES6 ! JavaScript is not fancy anymore"
date: 2014-10-19 12:04
comments: true
categories: [ES6, JavaScript]
author: Shidhin CR
---

>__21/10/2014 UPDATE:__ This post was updated ( see the new examples for arrow functions and template strings ).
>
> Thanks for the [comments](http://localhost:4000/blog/2014/10/19/welcome-es6-javascript-is-not-fancy-anymore/#disqus_thread) 
>and the [Reddit discussion](http://www.reddit.com/r/javascript/comments/2jr72y/welcome_es6_javascript_is_not_fancy_anymore/).


## 
For years, JavaScript was considered as a toy language. It was used for creating interactive web pages and could run only in the browser. But, things changed; JavaScript is used in both, the server and the browser these days. The simplicity of the language made it so popular that developers started using it for large and complex projects. 

However, new developers felt JavaScript was fancy at times. This was because of some of the known drawbacks in the language itself -- and the workarounds put in place for fixing them. For example, a developer from a "C" language background is more familiar to block level scoping, but will have difficulties understanding function scoping in JavaScript. Along with lexical scoping, variable hoisting and closures might seem fancy or difficult to understand as well.

<!--more-->

ES6, the future version, is going to give a vast makeover to JavaScript. The **TC39** committee (responsible for ES6 standardization) have taken care of most of the concerns about JavaScript, and now ES6 is getting a lot of new features added, and existing bad parts fixed. If you want to know about the JavaScript good vs bad parts, check out Douglas Crockford’s book [JavaScript, the Good Parts](http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742).

This post is **not** about the complete ES6 feature-set. Here, we will see some of the areas of JavaScript that look fancy, and how they are getting improved in ES6.

## ES6, all good parts?

Again, I am not saying ES6 has only good parts. To be honest, I don't know. ES6 is not completely ready for live applications. Design patterns will automatically evolve once the language is used extensively. Design patterns can distinguish between the good and bad parts.

As of now, most ES6 features are not supported by browsers (as they’re in draft). We use transpiler tools to compile ES6 code to ES5. While there are many tools available, Google’s [traceur](https://github.com/google/traceur-compiler) seems to be the most popular among them.

## 8 fancy things fixed in ES6 

Here are some interesting improvements done in ES6:

### Object.is for better comparison

New developers who learn JavaScript often stumble on the usage of `==` and `===`.  The `===` is a strict comparison operator where it checks the type of the operands also. For example, in this code:

```javascript
“0” == 0  // true

but

“0” === 0 // false
```

It is always recommended to use the `===` operator. However, there is an excepton to this; we cannot compare `NaN` using any of these operators. We need to use the global function `isNaN` to check if any variable has a `NaN` value or not.

```javascript
NaN == NaN  // false
NaN === NaN  // false

var result = isNan(NaN) // true
```

`Object.is` is an attempt to have a better comparison method. It is the same as the `===` operator, except it can compare `NaN` also.

```javascript
Object.is(0 ,”0”) // false
Object.is(0 ,0) // true
Object.is(NaN, NaN) // true
```
### Let for block scoping.

As opposed to languages like "C", JavaScript doesn't have block scoping. All variables inside a block are hoisted to its containing function (if any) or will be part of the global scope. 

```javascript
if(true){
  var a = 10;
  console.log(a); // 10
}
// outside the if block
console.log(a); // a is accessible here also and prints 10
```
Following is a popular example of this:

Assume that we have 10 anchor tags in an html page. We need to alert the index of each anchor tag whenever it’s clicked. Now, look at the code below:

```javascript
var anchors = document.getElementsByTagName(“a”);
for(var i=0,len=anchors.length; i<len; i++){
    anchors[i].onclick = function(){
        alert(i);
    };
}
```
If we think the above code works, we are wrong. It is the last value of `i` that is alerted. Here we will see **10** getting alerted.

We use a **closure** to fix this problem. Using closure, we can bind the right value of `i` to the onClick handler. See the code below:

```javascript
var anchors = document.getElementsByTagName(“a”);
for(var i=0,len=anchors.length; i<len; i++){
    anchors[i].onclick = (function(i){
        return function(){
            alert(i);
        };
    })(i);
}
```
Having a closure in the above code makes it unreadable and totally fancy to a new developer. Now we know why the word **"fancy"** is here.

ES6 introduces block scoping in JavaScript using the keyword `let`. If we write our first example, using `let`:

```javascript
if(true){
  let a = 10;
  console.log(a); // 10
}
// outside the if block
console.log(a) // Reference error: a is not defined
```

`let` also binds the scope of the variable to the current block. In our second example, we can use `let` to solve the scoping problem:

```javascript
var anchors = document.getElementsByTagName(“a”);
for(let i=0,len=anchors.length; i<len; i++){
    anchors[i].onclick = function(){
        alert(i);
    }
}
```
Now the above code should work as expected.

### Multi-line strings and string interpolations

Writing multiline strings is not so straightforward. The `\n` ( for newline ) has to be added where ever a line break is needed.

```javascript
var myString = 'Lorem ipsum \ndolor sit amet,\n\n\n consectetur adipisicing\n elit.';
```
ES6 introduces **template strings** for creating multiline strings. In ES6, we can write the above example like this:

```javascript
    var myString = `Lorem ipsum 
                    dolor sit amet,
                    
                    consectetur adipisicing
                    elit.`;
```
So, here, we can use "\`" (backtick) to create the strings.

Another interesting usecase of the template strings is variable interpolation. There is no native variable interpolation in ES5, but can be achieved by regular expressions or by manually appending the variable to the string. For example:

```javascript
// Using + operator 
var name = 'Tony';
var age = 20;
var greeting = 'Hi, I am '+name+' and my age is '+age;
console.log(greeting); // This prints 'Hi I am Tony and my age is 20'

// Using regular expressions
var greeting = 'Hi, I am %name% and my age is %age%'.replace(/%name%/g,name).replace(/%age%/g,age);
console.log(greeting); // This prints 'Hi I am Tony and my age is 20'
```
In ES6, the above code is really simplified, and can be written like this:

```javascript
var name = 'Tony';
var age = 20;
var greeting = `Hi, I am ${name} and my age is ${age}`;
console.log(greeting); // This prints 'Hi I am Tony and my age is 20'
```

### Fat Arrow functions for binding scope

Most new developers struggle to understand the `this` keyword in JavaScript. `this` is nothing but the execution context for a function, and for methods, `this` points to the object holding it. If the function is executed not as a method of an object, `this` will point to the global object (usually the window object).

`this` can be confusing many times. Look at the below example:

```javascript
var name = 'Tom';
var obj = {
    name: 'Jerry',
    sayName: function(){
        console.log(this.name);
    },
    greet: function(){
       setTimeout(function(){
           console.log('Hi,' + this.name );
       },100);
    }
};
obj.sayName(); // logs Jerry
obj.greet(); // logs Hi, Tom
```

When the `sayName` is executed as an object method, `this` was pointing to object itself, so `this.name` will output "Jerry". Inside the `greet` function, a `setTimeout` is used for delaying the function execution. `setTimeout` will invoke the function in global context, hence `this` will point to the global object.

We usually use `Function.bind` or `Function.call` or `Function.apply` to fix these kinds of problems.

```javascript
var name = 'Tom';
var obj = {
    name: 'Jerry',
    sayName: function(){
        console.log(this.name);
    },
    greet: function(){
       setTimeout(function(){
           console.log('Hi,' + this.name );
       }.bind(this),100);
    }
};
obj.sayName(); // logs Jerry
obj.greet(); // logs Hi, Jerry
```

ES6 added arrow functions to get rid of scoping issues. An arrow function will always lexically bind the `this` value to its surrounding environment. So the above code can be written in ES6 like this:

```javascript
var name = 'Tom';
var obj = {
    name: 'Jerry',
    sayName: function(){
        console.log(this.name);
    },
    greet: function(){
       setTimeout( ()=> {
           console.log('Hi,' + this.name );
       },100);
    }
};
obj.sayName(); // logs Jerry
obj.greet(); // logs Hi, Jerry
```

Here, inside the arrow function, `this` always points to the parent scope `this` value.

**Note:** _Since the arrow function is already bound to its execution context, we cannot apply .bind(), .call() or .apply() methods on it again._

### Destructuring

Destructuring is the process of assigning the property values of an object to a local variable. For example, in JavaScript, we can do this:

```javascript
    var myGlobalConfig = {
        apiUrl: 'www.google.com/api',
        data1: 'some value',
        data2: 'another value',
        methodType: 'POST'
        // ... etc
    };
    
    // and in one of our functions
    
    function makeAjaxRequest(config){
        var url = config.url;
        var method = config.methodType;
        var data = config.data2;
        $.ajax(url, method, data );
    }
    
    // call makeAjaxRequest
    makeAjaxRequest( myGlobalConfig );
```

In the above code, whenever a function requires arguments as multiple parameters, we need to extract values from our config object and pass them as parameters. This is a problem when we have a large number of values that need to be extracted.

ES6 allows direct destructing assignments. So we can write the above example in a simple way, like this:

```javascript
    var myGlobalConfig = {
        apiUrl: 'www.google.com/api',
        data1: 'some value',
        data2: 'another value',
        methodType: 'POST'
        // ... etc
    };
    
    // and in one of our function
    
    function makeAjaxRequest(config){
        var { url, methodType, data2 } = config;
        $.ajax(url, methodType, data2 );
    }
    
    // call makeAjaxRequest
    makeAjaxRequest( myGlobalConfig );
```

### Default Argument Values for Functions

One of the best features in ES6 is default arguments. As of now, we use the `||` to have default values for the function parameters. See the below code:

```javascript
    var myFunction = function(a, b, c){
        a = a || 10;
        b = b || 5;
        c = c || 8;
        return a*b*c;
    };
```
Here is the same code written in ES6:

```javascript
    var myFunction = function(a=10, b=5, c=8){
        return a*b*c;
    };
```
### Object method shorthands

Look at the below object creation pattern. In this, all the public method/property of an object will have their own private counterparts. For example:

```javascript
    var myObject = (function(){
        var sayName = function(){
            console.log('this is sayname function');
        };
        var greet = 'This is a greeting';
        return {
            greet: greet,
            sayName: sayName
        }
    })();
```
The above pattern is called **Revealing module pattern**. In ES6, it is much simplified by eliminating the column part. Thus, we can write the above code like this:

```javascript
    var myObject = (function(){
        var sayName = function(){
            console.log('this is sayname function');
        };
        var greet = 'This is a greeting';
        return {
            greet,
            sayName
        }
    })();
```

### The super keyword for invoking super class methods

JavaScript supports object oriented programming. But, implementing inheritance is tricky in JavaScript, as it is not supported natively. JavaScript even has the `super` keyword, however, it is not functional.

Most of the JavaScript frameworks those implement inheritance have a pattern like this:

```javascript
    var myBaseObj = {
        sayName: function(){
            // .. do something
        };
    };
    
    var mySubObj = Framework.extend(myBaseObj, {
        sayName: function(){
            this._super.apply(this,arguments);
            // .. do something else
        }
    })
```
These frameworks use `_super` (or sometimes `uber`) to invoke the parent method. This is because, `super` was a reserved keyword (for the future) in JavaScript, and is not allowed for use as an identifier or method. 

Now, the "future" has come. JavaScript's `super` keyword has been implemented in ES6. Now let's see how inheritance can be done:

```javascript
    var myBaseObj = {
        sayName: function(){
            // .. do something
        };
    };
    var mySubObj = {
        __proto__: myBaseObj,
        sayName: function(){
            super.sayName();
            // .. do something else
        }
    })
```

Again, all these are just a small part of the ES6 feature-set. There are so many interesting features like Promises, Classes, Generator, Iterators, etc. 

## Summary

ES6 is the most promising version of JavaScript. It has so many features; features we were waiting for for years. ES6 will definitely help writing more modular and less quirky code in JavaScript.

Here in this article, we have seen the problems with the current version of JavaScript, and how ES6 fixes the problems and the workarounds. Now, the entire ES6 feature-set is beyond the scope of this post, for that you can check out these awesome resources.

- https://github.com/lukehoban/es6features
- https://github.com/ericdouglas/ES6-Learning
- http://espadrine.github.io/New-In-A-Spec/es6/
- http://tc39wiki.calculist.org/es6/