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

Website performance is the hot topic these days. In fact, performance is becoming critical factor for websites. First, users really love fast loading websites. Second, it's going to affect the SEO page ranking. According to Google,  page load speed as one of the key parameter of their ranking algorithm. Thus, it is critical for websites to improve page speed performance not only just for their users, but also for winning against their competitors in Google search results.

And for the E-commerce websites, this will be super critical. If you're not clear,  see the below result from Amazon after improving their website page performance:

> For every 1 second of improvement they experienced up to a 2% increase in conversions. For every 100 ms of improvement, they grew incremental revenue by up to 1%

## Improving page load time

Browser spend most of the time for downloading the resources. If the resources ( JS, CSS ) are inside the `HEAD` tag, browser has to download all of them before parsing the HTML. Therefore, users won't be able to see anything on the screen till all the resources are downloaded.
<!--more-->
We can solve the HTML parsing problem by moving these resources to the bottom of the page; Just above the ending `BODY` tag. We can also apply the JavaScript lazy loading techniques to load the resources after the DOM is completely parsed and interactive. However, if we move any stylesheets from HEAD tag ( or lazyload stylesheets ), it will lead to a terrible user experience problem called **FOUC**. 

## FOUC  ( Flash of Unstyled Content )

**FOUC** is the abbreviation for Flash of Unstyled Content.  A **FOUC** can occur when the browser has already parsed the DOM, then apply the stylesheet later.  An user at this time will see a completely un-styled content till the stylesheet is downloaded and applied. This is the reason for including stylesheets in the `HEAD` tag considered to be a best practice. Because, if the resources are in the `HEAD`, browser has to download and apply all of them before parsing the DOM. 

Including the stylesheets in the `HEAD` is a best practice for good user experience. But, it hit the page load performance in the opposite direction. Since the browser has to download all the resources in the `HEAD` tag before any parsing, the page load time increases. This particular problem existed for years without any solution, and now, we have something known as **Critical Css** to fix it.

## Critical Css

Before move on to the Critical Css, we need to understand what is an **Above the fold content**. Because, it plays an important role about Critical Css. The **Above the fold content** is first html content ( markup ) user sees in the browser with out doing any scrolling. This markup varies between device to device, hence the  **Above the fold content** completely depends on the device's ( or browser's ) height and width.

Critical Css is the minimum css rules required for displaying the "**Above the fold content**" with out causing any **FOUC** to the users. Critical Css is also calculated page by page as the **Above the fold content** for different pages will not be the same. Once the Critical Css is generated for any page ( We'll discuss how to do it in the next section ), it will be embedded in the `HEAD` tag as inline style; Then, the original CSS files will be lazy loaded. This will have a good performance impact, as there is no need to wait for the CSS files to get downloaded before DOM parsing, hence no **FOUC**.

## Generating Critical Css

We cannot generate the Critical Css required for each page manually. There are lot of tools available for generating the Critical Css ( some of them are limited to NodeJS applications ). Here in this section, we'll see how to use those tools to generate Critical Css for our applications/websites.

#### NodeJS tools:

There are different npm modules available. Choose any of them based on your convenience.

1. [Critical](https://www.npmjs.com/package/critical) ( From Addy Osmani )
2. [Penthouse](https://www.npmjs.com/package/penthouse)
3. [CriticalCSS](https://github.com/filamentgroup/criticalCSS) ( From Filament group )
4. [Critical-CSS](https://www.npmjs.com/package/critical-css)

All we need to do is specifying the proper height and width -- as the tools need to calculate the ideal "**Above the fold content**" size.  It's better to check your analytics dashboard and come up with above the fold size ( height and width ) that can cover most of the devices. 

These tools are great for creating the Critical Css. However, they won't be good for creating Critical Css on the fly. To generate Critical Css in run time, there're some server modules available for Apache and Nginx. Let's check them out now.

#### Apache and Nginx modules

Google has released their pagespeed modules for both Apache and Nginx. See the links below:

1. [Pagespeed module Apache](http://modpagespeed.com/)
2. [Pagespeed module Nginx](http://ngxpagespeed.com/)

Pagespeed module is a set of filters written in C++, and critical-css is one among them. These filters dynamically transform the response from the server based on the configurations. More about nginx pagespeed critical css filter here:

[Prioritze Critical CSS Nginx](https://developers.google.com/speed/pagespeed/module/filter-prioritize-critical-css)

It should be fairly easy to configure the pagespeed module. However, if you are using CDN for serving your assets, you might need to add these filters in your configuration:

```sh
pagespeed InlineResourcesWithoutExplicitAuthorization
pagespeed MapProxyDomain
```

If you use Docker, there is a we have something for you. Here in Namshi, we open-sourced a docker container running nginx with pagespeed module. Checkout below link and read the documentation:

[Docker Node Nginx Pagespeed](https://github.com/namshi/docker-node-nginx-pagespeed)

## Summary

Fast loading websites leads to good user experience and better SEO ranking. Critical Css is a technique used for effectively loading stylesheets with out breaking the user experience. Using critical css will boost the page load performance drastically.  Here in this article, we discussed about how the Critical Css solved the classical **FOUC** problem and improves page loading speed and different tools used for generating Critical Css. 
