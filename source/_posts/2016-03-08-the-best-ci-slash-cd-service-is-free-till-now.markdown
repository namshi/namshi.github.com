---
layout: post
title: "The best CI/CD service Wercker CI is free (till now), use it"
date: 2016-03-08 09:02
comments: true
categories: 
---
Yesterday I did a short talk titled "The best CI/CD service is free (till now), lets use it". 
It was a quick rundown of [Wercker](http://wercker.com/) CI (Continuous Integration) features and how it works. 

{% img center /images/posts/geshan-wercker-talk.jpg %}

<!-- more -->

We are currently using [Travis CI](https://travis-ci.com/) for running our tests and our own open source
[Roger](https://github.com/namshi/roger) app to build docker images. The main objectives of the talk were:

* Suggest using Wercker CI to combine running our tests and building our containers in a sequence. This will prevent us from deploying broken builds

* Inform about a free service that can run 2 concurrent workers to build and deploy containers. It supports closed source projects too.

Here are the slides:

<center>
<iframe src="//www.slideshare.net/slideshow/embed_code/key/1AzqQ1yGYkX8af" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen>
</iframe> 
</center>
<br/>

We discussed how it's still in beta, which might cause issues in the future. 
We also discussed on how Continuous Delivery and Continuous Deployment are different.

Hope you like them :), comments and suggestions are always welcome.
