---
layout: post
title: "Improve Website Page Load Performance With Critical CSS"
date: 2015-04-08 16:41
comments: true
author: Shidhin CR
categories: 
- critical css
- nginx
- nodejs
---

![Critical Css](/images/critical-css.png)

Website performance is the hot topic these days. In fact, performance is becoming a critical factor for websites. First, users really love fast loading websites. Second, performance affects the SEO page ranking. According to Google, page load speed is one of the key parameters of their ranking algorithm. Thus, it is critical for websites to improve page speed performance - not just for improving users experience, but also for winning against their competitors in Google search results.

For E-commerce websites, this is super critical. If you're not clear, see the results from Amazon after improving their website page performance:

> For every 1 second of improvement they experienced up to a 2% increase in conversions. For every 100 ms of improvement, they grew incremental revenue by up to 1%

## Improving page load time

Browsers spend most of the time downloading resources. If the resources ( JS, CSS ) are inside the `HEAD` tag, the browser has to download all of them before parsing the HTML. Therefore, users won't be able to see anything on the screen till all the resources are downloaded.
<!--more-->
We can solve the HTML parsing problem by moving these resources to the bottom of the page; Just above the ending `BODY` tag. We can also apply the JavaScript lazy loading technique, to load the resources after the DOM is completely parsed and interactive. However, if we move any stylesheets from the `HEAD` tag ( or lazyload stylesheets ), it will lead to a terrible user experience problem called **FOUC**. 

## FOUC  ( Flash of Unstyled Content )

**FOUC** is the abbreviation of - Flash of Unstyled Content.  A **FOUC** can occur when the browser parses the DOM before applying the stylesheet. A user, at this point, will see completely un-styled content till the stylesheet is downloaded and applied. This is the why including stylesheets in the `HEAD` tag is considered to be a best practice. Because, if the resources are in the `HEAD`, the browser has to download and apply all of them before parsing the DOM. 

Including the stylesheets in the `HEAD` is a best practice for good user experience. But, it has an effect on the page load performance in a negative manner. Since the browser has to download all the resources in the `HEAD` tag before any parsing, the page load time increases. This particular problem has existed for years without any solution, but now, we have something known as **Critical CSS** to fix it.

## Critical CSS

Before moving on to Critical CSS, we need to understand what **Above the fold content** means. It plays an important role in Critical CSS. **Above the fold content** is the first HTML content ( markup ) a user sees in the browser without scrolling. This markup varies between devices, hence, **Above the fold content** completely depends on the device's ( or browser's ) height and width.

Critical CSS is the minimum css required for displaying "**Above the fold content**" with out causing any **FOUC** for the users. Critical CSS is also calculated page by page as the **Above the fold content** for different pages will not be the same. Once the Critical CSS is generated for any page ( We'll discuss how to do it in the next section ), it will be embedded in the `HEAD` tag as inline styles; then, the original CSS files will be lazy loaded. This will have good impact on performance, as there is no need to wait for the CSS files to get downloaded before DOM parsing, hence, no **FOUC**.

## Generating Critical CSS

We cannot generate the Critical CSS required for each page manually. There are lot of tools available for generating the Critical CSS ( some of them are limited to NodeJS applications ). Here in this section, we will see how to use these tools to generate Critical CSS for our applications/websites.

#### NodeJS tools:

There are different npm modules available. Choose any of them based on your convenience.

1. [Critical](https://www.npmjs.com/package/critical) ( From Addy Osmani )
2. [Penthouse](https://www.npmjs.com/package/penthouse)
3. [CriticalCSS](https://github.com/filamentgroup/criticalCSS) ( From Filament group )
4. [Critical-CSS](https://www.npmjs.com/package/critical-css)

All we need to do is specify the proper height and width - as the tools need to calculate the ideal "**Above the fold content**" size.  It is better to check your analytics dashboard and come up with an above the fold size ( height and width ) that can cover most of your website traffic. 

These tools are great for creating Critical CSS. However, they won't be good for creating Critical CSS on the fly. To generate Critical CSS in run time, there are some server modules available for Apache and Nginx. Let's check them out now.

#### Apache and Nginx modules

Google has released their pagespeed modules for both Apache and Nginx. See the links below:

1. [Pagespeed module Apache](http://modpagespeed.com/)
2. [Pagespeed module Nginx](http://ngxpagespeed.com/)

A pagespeed module is a set of filters written in C++, and critical-css is one of them. These filters dynamically transform the response from the server based on the configurations. More on nginx pagespeed critical css filters here:

[Prioritze Critical CSS Nginx](https://developers.google.com/speed/pagespeed/module/filter-prioritize-critical-css)

It should be fairly easy to configure the pagespeed module. However, if you are using a CDN for serving your assets, you might need to add these filters in your configuration:

```sh
pagespeed InlineResourcesWithoutExplicitAuthorization
pagespeed MapProxyDomain
```

If you use Docker, we have something for you. Here in Namshi, we open-sourced a docker container running nginx with pagespeed module. Check out this link and read the documentation:

[Docker Node Nginx Pagespeed](https://github.com/namshi/docker-node-nginx-pagespeed)

## Summary

Fast loading websites lead to a good user experience and better SEO ranking. Critical CSS is a technique used for effectively loading stylesheets without affecting the user experience. Using Critical CSS will boost page load performance drastically. Here in this article, we discussed how Critical CSS solves the classic **FOUC** problem and improves page speed, the different tools used for generating Critical CSS, and how we open-sourced a docker container running nginx with pagespeed module.