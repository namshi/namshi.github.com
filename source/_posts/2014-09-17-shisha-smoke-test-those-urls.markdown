---
layout: post
title: "Shisha, smoke test those URLs!"
date: 2014-09-17 10:00
comments: true
published: true
author: Mohamad Hallal
categories: smoke-testing, npm, nodejs
---

Here comes Shisha! Yet another NodeJs based application from Namshi! Shisha is a smoke testing module,
what is smoke testing? Read about it [here](http://en.wikipedia.org/wiki/Smoke_testing_\(software\)).
In short, smoke testing is a term coming from Electrical Engineering, where an Electronic chip is simply tested by
plugging it into an electric source and observing if smoke comes out! How does shisha relate to this? Well,
just give shisha a list or a file of URLs and expected HTTP status code for each URL, and it will assert the expected
status codes and show you back a report! It serves as a quick and fast testing platform for your website/APIs.
Instead of waiting for a unit or an integration test to be prepared and run, simply set shisha to run on your URLs,
it is fast and simple!

<!-- more -->

#Install it

Install shisha globally from [NPM](https://www.npmjs.org/package/shisha)

```
npm install -g shisha
```

#Prepare it and smoke it

Create a text file named `.smoke` where all your test cases will reside.

Fill it as following:

```
http://example.org 200
http://example.org/not-there 404
```

From the directory where your `.smoke` file is, run `shisha` and observe the shisha smoking!

Here is a video illustration:

{% video http://tech.namshi.com/videos/example.mp4 640 640 %}

#Features

You can:-

* Pass a path to any text file containing the right URL status code mapping using `--smoke` argument

```
shisha --smoke /path/to/text/file
```

* Pass locals to be replaced in your text file

```
shisha --domain example.org --protocol http
```

so your text file would look something like this

{% raw %}
```
{{ protocol }}://{{ domain }}/path 200
```
{% endraw %}

Here is how locals work:

{% video http://tech.namshi.com/videos/locals.mp4 640 640 %}

* Easily extend shisha in your code as follows:

```
npm install --save shisha
```

then, in your code:

```
var shisha = require('shisha');
```

you will have access to the `smoke` method which accepts the following arguments:

1. data source, which can be a path to a text file, a list or an object.
2. A list of locals to be replaced in the placeholders of the text file.
3. A callback which accepts the report argument where the set of smoking results are parsed.

```
 shisha.smoke('/path/to/a/text/file', options, callback);
```

or

```
shisha.smoke(
    [
        {
             url: 'http://example.org',
             status: 200
         },
         ...,
         ...
    ], locals, callback
);
```

or

```
shisha.smoke(
    {
        'http://example.org': 200,
        'http://example2.org': 404,
        ...,
        ...
    }, locals, callback
);
```

`locals` as mentioned before, is a list of placeholders to be replaced in the smoke file

```
shisha.smoke(urls, {protocol: 'https', domain: 'namshi.com'}
```

`callback` is a function called when smoking is done!

if you do not have any locals, you can simply omit it.

```
shisha.smoke(urls, callback);
```

Happy smoking!