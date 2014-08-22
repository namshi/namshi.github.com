---
layout: post
title: "node-file-ensure, a small filesystem utility to make sure a file is there"
date: 2014-08-22 19:42
comments: true
categories: [JavaScript, NodeJS, OSS, Open Source]
author: Alessandro Nadalin
---

This weekend we've come up with a small utility
that will make our life easier, and hopefully **yours**
as well!

<!-- more -->

We use cascading mechanisms for our configurations, so, for
example, we define the config in a few different files:

* `base.yml`, that has all the common configurations
* `dev.yml`, which is for local environments
* `staging.yml`, which is for our staging environment
* `live.yml`, well...you guessed it!

So, for example, a `live.yml` might look like:

``` yaml
domain:   'namshi.com'
```

whereas the base one has many more values:

{% raw %}
``` yaml
apis:
  version:   'v1'
  domain:    'api.{{ domain }}'
  baseUrl:   'https://{{ apis.domain }}'
```
{% endraw %}

There's a problem with this approach: if one of the developers
wants to change a value in his `dev.js` git will tell him that
the file has changed, which is quite annoying!

To overcome this we always put it in the `.gitignore`:

```
dev.yml
```

and instead commit a `dev.yml.example` file that has the
skeleton of the file, asking everyone, when booting the application
for the first time, to copy that file to a `dev.js`.

Things work flawlessly until you need to do this 10 times
(I mean, in 10 projects, like we do) or when you forget this step
during the installation and waste a lot of time trying to debug why
the application is not running correctly.

To overcome this annoying issue we decided to develop a small utility,
[node-file-ensure](https://github.com/namshi/node-file-ensure), that
does all of this for you:

``` javascript
var ensure = require('file-ensure');

ensure('./config/dev.yml', {from: './config/dev.yml.example'});
```

Simple as that!

`ensure` will make sure to create an empty file if it doesnt find
the one you specified:

``` javascript
ensure('./config/dev.yml');
```

and is able to copy the contents from another file,
like in the first example above:

``` javascript
ensure('./config/dev.yml', {from: './config/dev.yml.example'});
```

If you need to run any check after `ensure` has run,
you can provide a callback:

``` javascript
ensure('path/to/config/dev.yml', {src: 'path/to/config/dev.yml.example'}, function(err){
  console.log(fs.readFileSync('path/to/config/dev.yml').toString() === fs.readFileSync('path/to/config/dev.yml.example').toString());
});
```

That's it! Tests run through mocha and we've set them up
on [travis](https://travis-ci.org/namshi/node-file-ensure),
to be on the safe side :)

If you have patches, suggestions or rants...feel free to open
an [issue on GitHub](https://github.com/namshi/node-file-ensure/issues)!



