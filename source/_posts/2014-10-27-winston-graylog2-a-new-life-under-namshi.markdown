---
layout: post
title: "winston-graylog2: a new life under Namshi"
date: 2014-10-27 10:32
comments: true
categories: [JavaScript, nodejs, library, open source, Namshi]
author: Luciano Colosio
---

As our herd of node.js apps grows, we soon needed a way to keep an eye on them.
Having already in out tool belt a [graylog2](http://www.graylog2.org/) facility in place, looking for a way to leverage it was the most natural thing to do.

Because of its nice modular `transports feature` our node-logging weapon of choice is [winston](https://github.com/flatiron/winston), the `graylog2` transport library looked a bit alone though and in need of a maintainer.
In our usual **give back** spirit we choose to adopt the lib instead of publishing a new one, and today we merged our 1st PR! :)

As for now our intervention was isolated to move away from the custom GELF protocol implementation, relying instead on the [node-graylog2](https://github.com/Wizcorp/node-graylog2) library:
they'll most likely do a better job than us on the protocol side, and we'll be able to focus more on the winston integration.
We also started introducing some basic tests: definitely some better work can be done on this side ;)

So here it is: [winston-graylog2](https://github.com/namshi/winston-graylog2)
and here's briefly how you use it:

the usual
```bash
npm install winston-graylog2
```
and then

```javascript
  var winston = require('winston');
  winston.add(require('winston-graylog2'), options);

```

or

```javascript
var WinstonGraylog2 = require('winston-graylog2');
var logger = new(winston.Logger)({
        exitOnError: false,
        transports: [new(WinstonGraylog2)(options)]
      });
```

Enjoy! :)

Publishing something new is always a great feeling, and it's even more fulfilling when it involves not letting a project die :)<br> So let us know if you like it and share the love with some RP!!!! ;)