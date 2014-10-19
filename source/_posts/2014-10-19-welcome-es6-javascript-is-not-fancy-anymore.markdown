---
layout: post
title: "Welcome ES6 !! JavaScript is not fancy anymore"
date: 2014-10-19 12:04
comments: true
categories: [ES6, JavaScript]
author: Shidhin CR
---

For years, JavaScript was considered as a toy language. It was used for creating interactive web pages and could run only in the browser. But, things changed; JavaScript is used in both server and browser these days. The simplicity of the language made it so popular, and developers started using it for large and complex projects. 

Still, however, new developers felt JavaScript is fancy at sometimes. The fanciness is because of some of the known drawbacks in the language itself -- and the workarounds put in place for fixing them. For example, a developer from the "C" language background is more familiar to block level scoping, but will have difficulties understanding the lexical scoping in JavaScript. And, all these lexical scoping, variable hoisting, closures seems fancy stuff for him.

ES6, the future version, going to give a vast makeover to JavaScript. The **TC39** committee ( responsible for ES6 standardization ) have taken care most of the concerns about JavaScript, and now ES6 is getting lot of new features added, and existing bad parts fixed. If you want to know about the JavaScript good vs bad parts, checkout Douglas Crockford’s book [JavaScript, the Good Parts](http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742).

This post is **not** about complete ES6 feature-set. Here, we'll see some of the areas of JavaScript that looks fancy, and how they're getting improved in ES6.

<!--more-->

## ES6, all good parts ?

Again, I am not saying ES6 has only good parts. To be honest, I don't know. ES6 is not completely ready for live applications. Design patterns will automatically evolve once the language is used extensively. Design patterns can tell which one is the bad part or good part. 

As of now, most of the ES6 features are not supported by browsers ( as they’re in draft ). We use transpiler tools to compile ES6 code to ES5. There are many tools available, but Google’s [traceur](https://github.com/google/traceur-compiler) is the popular among them.

## 8 fancy things fixed in ES6 

Below are some interesting improvements done in ES6.

**Object.is for better comparison**

New developers who learn JavaScript often get stumbled on the usage of `==` and `===`.  The `===` is a strict comparison operator where it checks the type of the operands also. For example, in the below code:

```javascript
“0” == 0  // true

but

“0” === 0 // false
```

It’s always recommended to use `===` operator. However, there is an excepton to this. We cannot compare `NaN` using any of these operators. We need to use the global function `isNaN` to check if any variable is holding a `NaN` value or not.

```javascript
NaN == NaN  // false
NaN === NaN  // false

var result = isNan(NaN) // true
```

`Object.is` an attempt to have a better comparison method. It is same as the  `===` operator, except it can compare `NaN` also.

```javascript
Object.is(0 ,”0”) // false
Object.is(0 ,0) // true
Object.is(NaN, NaN) // true
```
**Let for block scoping.**

As opposed to the languages like "C", JavaScript doesn't have block scoping. All variable inside a block are hoisted to its containing function ( if any ) or will be part of the global scope. 

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
If we think the above code works, we're wrong. It's the last value of `i` gets alerted always. Here we'll see **10** getting alerted always.

We use a **closure** to fix this problem. Using closure, we can bind the right value of `i` to the onclick handler. See the code below:

```javascript
var anchors = document.getElementsByTagName(“a”);
for(var i=0,len=anchors.length; i<len; i++){
    anchors[i].onclick = (function(){
        return function(){
            alert(i);
        };
    })(i);
}
```
Above code now became unreadable and totally fancy to a new developer. Now we know why the word **"fancy"** is here.

ES6 introduces block scoping in JavaScript using a keyword `let`. If we write our first example, using `let`:

```javascript
if(true){
  let a = 10;
  console.log(a); // 10
}
// outside the if block
console.log(a) // Reference error: a is not defined
```

`let` also binds the scope of the variable to current block. In our second example, we can use `let` to solve the scoping problem:

```javascript
var anchors = document.getElementsByTagName(“a”);
for(let i=0,len=anchors.length; i<len; i++){
    anchors[i].onclick = function(){
        alert(i);
    }
}
```
Now the above code should work as expected.

**Multi-line strings and string interpolations.**

Writing multiline strings is not so straightforward. The `\n` ( for newline ) have to be added where ever a line break is needed.

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
So, here, we can use "\`" ( backtick ) to create the strings.

Another interesting usecase of the template strings is variable interpolation. There is no native variable interpolation in ES5, but can be achieved a similar by regular expressions or by manually appending the variable to the string. For example,

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
In ES6, the above code is really simplified. Same can be written like this:

```javascript
var name = 'Tony';
var age = 20;
var greeting = 'Hi, I am ${name} and my age is ${age}';
console.log(greeting); // This prints 'Hi I am Tony and my age is 20'
```

**Fat Arrow functions for binding `this`**

Most of the new developers get struggled to understand `this` keyword in JavaScript. `this` is nothing but the execution context for a function, and for methods, `this` points to the object holding it. If the function is executed not as a method of an object, `this` will point to the global object ( usually the window object).

`this` can be confusing in many times. Look at the below example:

```javascript
var name = 'Tom';
var obj = {
    name: 'Jerry',
    sayName: function(){
        console.log( this.name );
    }
};
obj.sayName(); // logs Jerry
var sayName = obj.sayName;
sayName(); // logs Tom
```

When the `sayName` is executed as an object method, `this` was pointing to object itself; but when it's executed as a normal function, `this` points to the global window object.

We usually use either `Function.bind` or `Function.call` or `Function.apply` to fix these kind of problems.

```javascript
var name = 'Tom';
var obj = {
    name: 'Jerry',
    sayName: function(){
        console.log( this.name );
    }
};
obj.sayName(); // logs Jerry

// Using call or apply
var sayName = obj.sayName;
sayName.call(obj); // logs Jerry
sayName.apply(obj); // logs Jerry

// Using bind
var sayName = obj.sayName.bind(obj);
sayName(); // logs Jerry
```

ES6 added arrow functions to get rid of the scoping issues. An arrow function will always lexically binded `this` value to it's surrounding environment. So the above code can be written in ES6 like this:

```javascript
var name = 'Tom';
var obj = {
    name: 'Jerry',
    sayName: => {
        console.log( this.name );
    }
};
obj.sayName(); // logs Jerry
var sayName = obj.sayName;
sayName(); // logs Jerry
```
**Note:** _Since the arrow function is already bound its execution context, we cannot apply .bind(), .call() or .apply() methods on it again._

**Destructuring**

Destructing is the process of assigning the property values of an object to local variable. For example, in JavaScript, we do like this:

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
        var url = config.url;
        var method = config.methodType;
        var data = config.data2;
        $.ajax(url, method, data );
    }
    
    // call makeAjaxRequest
    makeAjaxRequest( myGlobalConfig );
```

In the above code, whenever a function requires arguments as multiple parameters, we need to extract values from the our config object and pass them as parameters. This is a problem when we have large number of values needs to be extracted.

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

**Default Arguments Values for Functions**

One of the best feature in ES6 is default arguments. As of now, we use the `||` to have default values for the function parameters. See the below code:

```javascript
    var myFunction = function(a, b, c){
        a = a || 10;
        b = b || 5;
        c = c || 8;
        return a*b*c;
    };
```
Here is the same code will be written in ES6:

```javascript
    var myFunction = function(a=10, b=5, c=8){
        return a*b*c;
    };
```
**Object method shorthands**

Look at the below object creation pattern. In this, all the public method/property of an object will have its own private counterpart. For example,

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
The above pattern is called **Revealing module pattern**. In ES6, it's much simplified by eliminating the column part. Thus,we can write the above code like this:

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

**The super keyword for invoking super class methods.**

JavaScript supports object oriented programming. But, implementing inheritance is tricky in JavaScript, as it's not supported natively. JavaScript even has the `super` keyword, however, it's not functional.

Most of the JavaScript frameworks, those implemented inheritance, have a pattern like this:

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
These framework used `_super` ( or sometimes `uber` ) to invoke the parent method. This is because, `super` was a reserved keyword ( for future ) in JavaScript, and it's not allowed to use it as an identifier or method. 

Now, the time has come; the "future" mentioned in previous section is, ES6. JavaScript has `super` implemented in ES6. Now let's see how inheritance can be done:

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

Again, all these are just a small part of ES6 featureset. There are so many interesting features like Promises, Classes, Generator and Iterators ..etc. 

## Summary

ES6 is the most promising version of JavaScript. It has so many features, those,  we were waiting for years. Definitely, ES6 will help writing more modular and less quirky code in JavaScript.

Here in this article, we have seen the problems with the current version of JavaScript, and how ES6 improved to fix them and workarounds. Now, the entire ES6 feature sets are beyond the scope of this post, for that you can also checkout these awesome resources.

- https://github.com/lukehoban/es6features
- https://github.com/ericdouglas/ES6-Learning
- http://espadrine.github.io/New-In-A-Spec/es6/
- http://tc39wiki.calculist.org/es6/