---
layout: post
title: "MockServer, Effortless API Mocking Library in Node Js"
date: 2014-06-13 04:22
comments: true
categories: [API, mock, front-end, node]
author: Mohamad Hallal
---

Namshi Tech Team is happy to roll-out a new node-based library!

We present you [MockServer](https://github.com/namshi/mockserver), a library that allows you to easily mock your APIs' plain HTTP responses.

<!-- more -->

The main benefit of this library is to allow developers to focus on what is important to them, and in this case,
`mockserver` allows front-end developers to focus their efforts on creating awesome front-end clients without having to worry about the
availability of a complete and running API.

The use of this library can be appreciated by developers following a [Front-End First](http://www.frontendfirst.com/)
methodology where development efforts initially focus on front-end clients rather than the back-end structure and tools.
Front-end first suggests that front-end developers have their mocked servers return expected or anticipated HTTP responses
based on the available specification instead of making an actual call to the API.
Another benefit is to allow development for both front-end and back-end in parallel.

In short, this library basically removes the dependency that the front-end development requires from the backend.

##Usage
Create an empty directory inside your project, `mkdir api`

To start mocking your HTTP responses, you will need to have a sub-directory were all your mocks shall reside. In order to do so,
in your `api` folder run `mkdir mocks` and that's where all plain text HTTP responses will reside.

Inside your `api` directory, run

```
npm install mockserver
```

Create a new js file, say, `api.js` that has the following:

```javascript
var http       = require('http'),
    mockserver = require('mockserver');

http.createServer(mockserver('./mocks').listen('9001');
```

That's it! Now navigate from your terminal to the `api` directory and run `node api.js`. This will create a node server that
listens to port `9001`.

Now, you should point your front-end client to `http://localhost:9001` instead of the actual api url.

##Mocks
Now, your node server is running and your client can start making requests and getting back mocked HTTP responses.

How do you create the mocks?

Each request relative url path, HTTP method combination resides in a separate text file inside your mocks folder.
The naming of those files goes as follows: `path_METHOD`, `path` is the relative url path without any slashes, i.e, `/test`
should be `test`. `METHOD` is the upper-cased HTTP method (`GET`), so, the file name is `test_GET`.

The contents of the mocked file should be a valid HTTP response text, as follows:

```
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8

<html><body>Hello World</body></html>
```

Now, whenever your client requests `http://localhost:9001/test`, `mockserver` will return that plain HTTP response you defined
in `test_GET`.

##Header Variation
Mockserver also supports different responses for the the same request path, method pair. You can make use of this feature by manually setting a
`mockserver-variation` header in your requests. The value of this header shall be appended to the mocked file name as follows: `path_METHOD_variation`.

Here is where you are able to handle various server HTTP responses for your request (200, 400, 500, 403, etc..).
Simply set `mockserver-variation` to `500` and the file from our previous example should be named `test_GET_500`.


##Conclusion

Although simple and straight forward, the idea behind `mockserver` is quite powerful. It is mostly suited for a Front-end First methodology, some of the
benefits include parallel front-end and back-end development and the decoupling of the Front-end and back-end during the development phase.

It would be great if you can take the time to try it out and let us know what you think!