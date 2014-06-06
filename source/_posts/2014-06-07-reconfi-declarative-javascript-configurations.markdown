---
layout: post
title: "Reconfig, declarative JavaScript configurations"
date: 2014-06-07 00:50
comments: true
categories: [JaaScript, open source, Namshi]
author: Alessandro Nadalin
---

We recently published a new library that let's you
deal with configurations, in JavaScript, in a very
elegant way.

<!-- more -->

The library is called [Reconfig](https://github.com/namshi/reconfig) and it's available
through [NPM](https://www.npmjs.org/package/reconfig) (if you need on the browser we highly
recommend [browserify](http://browserify.org/)): it's really
small and simple, and thought to keep your
configuration as elegant, simple and declarative as possible.

With reconfig you can do stuff like:

{% codeblock lang:javascript %}
{% raw %}
var reconfig = require('reconfig');

var config =  new reconfig({
    greet: 'Hello :who!',
    sayhi: '{{ greet }}'
    countries: {
    	uae: {
    		population: '2M',
    		nationalDay: 'December 2nd'
    	}
    	// ...
	}
});


// Retrieve a deep config value
config.get('countries.uae.nationalDay'); // December, 2nd

// Pass parameters to the configuration
config.get('greet', {who: 'John'}); // Hello, John!

// Reference other configuration parameters
config.get('sayhi', {who: 'John'}); // Hello, John!

// Fallback values
config.get('something', {}, 'invalid'); // invalid
{% endraw %}
{% endcodeblock %}

We're probably gonna look into porting the same to
PHP (another big player in our stack) so...stay tuned!