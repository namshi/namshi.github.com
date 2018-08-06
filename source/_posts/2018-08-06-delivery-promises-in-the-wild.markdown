---
layout: post
title: "Delivery promises in the wild"
date: 2018-08-06 11:57
comments: true
categories: [feature]
author: Carles Iborra
---

We recently launched a new feature which we internally call **“delivery promises”**. This feature informs our users the expected delivery lead time of individual products, based on their location. Users select their location from a drop down list and a timer counts down to the next available delivery dispatch time. This allows users to know how much time they have to place an order to receive their item at the next earliest possible delivery date.

{% img center /images/dp.png %}

<!-- more -->

E-commerce has made great technological strides in the last decade. There’s no doubt in anyone's mind that the paradigm shift of what the experience of  a “purchase” is has already taken place. E-commerce will only get larger, while brick & mortar will continue to dwindle. Yet all of this innovation still fails to recreate the sensations of instant gratification most shoppers feel at the checkout aisle. This gap in the process can be taken advantage off. By giving users a determined and relatively quick delivery date such as same day or next day delivery, we can bridge the gap just enough to provide semi-instant gratification. This semi-instant gratification, is enough to pass off as a reward to entice users into completing the purchase within a certain time frame to remain eligible for it.

Yet, this feature is a double edged sword. On the one hand, you can increase conversion rates and customer satisfaction when everything works well. On the other, users are much more irate when delivery is not made at the expected times.

In order to test the success and impact of this project we had to benchmark it against a few **KPI’s**.

We decided the best would be to track how our:

1. Products-added-to-cart % changed
2. The change in average session duration for users that checked out products
3. The overall conversion rate
4. The % of orders that got delivered within the promised date
5. The change in delivery related inquiries our customer service team received.

We decided to roll out slowly, segmenting by platform and geographical region. We started first on our web mobile platform and then slowly rolled it out to our apps all within certain geographical regions where we could ensure a higher minimum delivery **SLA**.

Our goal for the UI was to make the expected delivery information instantly accessible and visible, without compromising on more important information like product image, description, price and available sizes. Keeping the natural flow of the page is critical.

To achieve this goal, we added the feature section right after the product image/details section; where we show 3 pieces of information:

1. Live countdown timer that counts down to the next available delivery dispatch for that location.
2. City selection dropdown field for users to select their location.
3. Estimated delivery time as per the selected city

{% img center /images/dp1.png %}

We also added this information in our cart view popup to keep users engaged and informed about the expected delivery dates for their orders.

{% img center /images/dp2.png %}

Initially, we wanted to add the feature to our checkout page too. However, we found that it won’t be possible because we currently take user delivery addresses in open input text fields. Users can enter any text to describe their locations including cities, hence, we could not query the expected delivery service without a properly formatted input which would be in the form of a predefined set of cities. Goes to show that something as simple as a field type could be a blocker for a feature to work!

One of the challenges we faced was to find a way to display the most accurate delivery information as fast as possible and also customizable on the product level.

Collaboration with the ops team and understanding their delivery challenges was critical in the development of this feature. Despite this only being a forecast, our users would view this as a promised commitment. If a customer reads and believes that his order will arrive the same day, receiving it late may result in a tremendous loss of good-will.

Due to this risk, we created an internal tool for our warehouse and operations team. This tool allows our teams to change the delivery lead time for different locations on the fly. It also allows for the changes in delivery cut-off times. At any point if we receive an overwhelming amount of orders they would be able to change the lead times and/or cut off times within seconds.

One important issue that we faced was with time zones. In order to provide an accurate delivery promise we need to know where the customer is located and where we have the product stored. Each one can be in different time zones which makes the logic harder. Imagine that you send a product from GMT+4 to your customer but they’re living in GMT+3, you know that the delivery will take 1 hour and you send it at 8 a.m. so you tell to your customer that he will receive it at 9 a.m. but actually he will receive it at 8 a.m. As your 9 a.m. is their 8 a.m.
One way to solve this is to tell the customer how long it will take instead of specifying the delivery date, for example: “in 1 hour and 15 minutes”, but for longer periods of time this becomes less useful. Another way is to yield this responsibility to the frontend as they know the actual timezone of the customer. So only sending the amount of time it will take for us to deliver, will allow for the front end to specify the delivery date perfectly to the user.

Another critical point for this feature is that it’s present in all parts of our customers critical path, so in addition to deciding how to implement it we had to rack our minds to decide where we needed to show it. Implementing this in our catalog proved challenging in terms of maintaining performance. In order to reduce the footprint we had to think carefully as to how we implemented and managed the cache. We did load testing to see how performance was affected. Our results showed that we increased the response time by between 1 to 5 milliseconds. This was not ideal, but still acceptable.

**We hope this feature helps you, and you get your packages on time!**

*This post is a joint effort between the brains behind this feature: [Carles Iborra Sanchez](/team#Carles%20Iborra),
[Ammar Rayess](/team#Ammar%20Rayess) and Razek Amir.*
