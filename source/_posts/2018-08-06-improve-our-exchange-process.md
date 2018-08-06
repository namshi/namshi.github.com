---
layout: post
title: "Improving our return process"
date: 2018-08-06
comments: true
categories: [return, exchange, process]
author: Ayham Alzoubi
---

One of the biggest challenges for e-commerce portals is to be able to deliver the same, or even a better, kind of experience physical retailers offer in terms of size exchanges. When you want to return a jacket at the Zara store next door, you simply walk to the store, ask for a larger size, and swap your return with the new, larger jacket — in case that size is out of stock you will immediately be refunded. In any case, it’s [instant gratification](https://en.wikipedia.org/wiki/Gratification#Instant_and_delayed_gratification).

Usually, in an e-com transaction, a 3rd party courier is involved in the delivery of the goods, and the same applies when customers wants to return, or exchange an item they purchased.

This leads to an interesting dichotomy, as e-commerce should, in theory, ease the process: but by waiting for the courier to collect the original item and deliver it back to the store; let the store confirm the return is in good condition, hand the new item to the courier and wait for the courier to deliver it to you…the **customer experience suffers**. This process can take weeks, and can be definitely improved.

At the beginning of this year, we focused our attention towards our exchange process (when you bought an M but want to replace it with an L), in order to make it seamless for customers to exchange items they purchased at [Namshi](https://www.namshi.com). We believe we’ve made strides in this process and wanted to share with you the changes we’ve implemented, our rollout strategy and the challenges we’ve faced along the way.

The new process we rolled out allows customers to request a new size **without having to place a new order**, **without having to worry about the new size going out of stock**, and have it delivered to their doorstep, in some cases, **less than a day**.

Let’s get to it.

******Our original exchange process:**

Our original exchange process had pretty basic flow.  A customer would place an order with some items, and if they decided to return any item(s), a return would have to be initiated via the account section. Our driver would then head over to collect the items that needed to be returned. Once those items reached our warehouse, we would then refund the amount owed back either as [Namshi](https://www.namshi.com) credit or as a credit / debit card refund. At this point, the customer could place a new order for the new size.

This approach seemed pretty dated as our customers suffered because of the extended time frame of the whole process, during which the size they wanted instead could have run out, but also because during this time, the price of the product may have fluctuated. This would result in them having to pay this new price.

**So… We added exchanges!**

At first, It wasn’t clear how we were going to implement exchanges. We knew we had all the components for creating an exchange in place, so it was a matter of connecting the dots to produce a single process that makes it easy for the customer to create an exchange with just a few clicks.

Therefore, we had to make sure of a few things:


1. We have the relevant replacement items in stock.
2. The returned item always reached our warehouse before we released the new shipment.
3. The customer won’t have to pay anything extra, even if the exchange item’s price increased.
4. The customer’s wallet balance should never become negative.


We created a new API that could handle both normal returns and exchanges. This API proxies all normal return requests to the returns service, while handling exchange requests also. In the case of exchanges, the API first creates the exchange order, this guarantees the stock to be reserved. Then a return request is created which is associated with the newly created exchange order. The customer just has to wait for the courier to come and pick up the original item. Once the item is picked and returned to the warehouse, we confirm the exchange order and export it for shipment.

For exchanges, we handled the payment of the exchange order via our customer wallet. Normally we charge the customer wallet as soon as an order is placed. However in this case, we hold on to charging the wallet until the returned item is refunded back to the wallet. This ensures that we only use the refunded money to pay for the exchanged item. This also prevents a customer’s wallet balance from becoming negative since we refund first then charge the wallet. These actions are clearly reflected in the customer’s credit section.

We also had to account for unhappy flows; For instance a customer may cancel the return, so there would be no item to be picked up. In this case we cancel the exchange item as well, because there may be no funds available in the customer’s wallet to cover for the new item. Also we can fairly assume that since the customer canceled the return, they probably changed their mind about the exchange.

Additionally we created a cron job that was responsible for canceling any exchange orders if we didn’t receive the original item (for whatever reason) after 2 weeks, of creating the exchange request. We did this so that we don’t block our stock for an extended period of time.

Once we implemented exchanges across our markets, we then decided to roll out doorstep exchanges to further improve our the exchange process.

**How did we roll out doorstep exchanges?**

At the doorstep exchanges entailed our courier agent going to a customer’s delivery address, picking up the original item and handing over the new product in one go!

In order to do this, we built a flag in our systems to recognize these swap requests. Once this was done, just as with exchanges, we began to reserve items as soon as we received a request for a doorstep exchange. This shipment was released to the same courier agent who was expected to collect the original item.

Since at the door exchanges was a novel concept in the region, we conducted an extensive training process for our courier agents. We trained them to identify and match items so that items returned back to us matched the ones we were handing over to our customers. We also had to ensure that our agents could handle scenarios where our customers were only returning some items from an order, receiving other orders at the same time, changing their mind regarding their swap requests when the courier agent arrived etc.

Once we were confident that our in-house courier agents could handle doorstep exchanges, we began rolling it out incrementally to customers across UAE. We began with Sharjah, followed by Fujairah, Ajman, Ras al Khaimah, Al-Ain, Abu Dhabi and finally Dubai. We rolled out doorstep exchanges successfully across the UAE within the course of just 5 weeks!

**Feedback! Feedback! Feedback!**

The end goal is always customer satisfaction!

For this purpose, we ran 3 surveys to gauge how our customers felt about our original returns service, exchanges and at the doorstep exchanges.

We wanted to learn whether customers were satisfied with these services and also if there was anything we could do to improve these further.

**Returns:**


![](https://d2mxuefqeaa7sj.cloudfront.net/s_BDC44FD999A9BF03CF62C61FC72DBA66D7B2A7053E24A6DFAF36A892D63BA5D8_1531224354306_Returns+results.PNG)


We found out that our customers were pretty satisfied with the original returns process with a combined satisfaction rate of very satisfied and satisfied customers at 75%.

For those customers who expressed that they were dissatisfied with our service and if they gave us feedback as to why they were unhappy, we analyzed their responses to see if we could further improve the returns process and factor those suggestions in.

**Exchanges:**

Given that we just launched exchanges across all our markets, we were pretty excited to hear back from customers about how they felt about this new venture.


![](https://d2mxuefqeaa7sj.cloudfront.net/s_BDC44FD999A9BF03CF62C61FC72DBA66D7B2A7053E24A6DFAF36A892D63BA5D8_1531226124692_exchange+results.PNG)


and voila!

Our customer satisfaction rate shot up to 88%.

Our customers loved this new feature! Exchanges now enabled us to reserve items for customers and ensure that they get the same deals, discounts and prices that they purchased their items for.

We received some feedback from customers regarding our process seeming too long. Our courier partner would collect the original item(s) from the customer and we would dispatch the exchange item(s) once we received the original one(s).

This feedback tied in neatly with our next initiative… At the door exchanges!!!

**At the door exchanges**

We wanted to make sure that our customers received the best possible service from our end. So, given the feedback from our customers and our capacity, we decided to launch at the doorstep exchanges.

These entailed our in-house couriers heading to a customer’s delivery address, collecting the original item and handing over the exchange item in one go!

Once we launched this service, we ran another survey to see what our customers thought:


![](https://d2mxuefqeaa7sj.cloudfront.net/s_BDC44FD999A9BF03CF62C61FC72DBA66D7B2A7053E24A6DFAF36A892D63BA5D8_1531234467580_Swap+results.PNG)


We reached a 92% satisfaction rate with at the doorstep exchanges.

Both regular exchanges and at the doorstep exchanges were a success with our customers!

**What we wanted to achieve:**

By enabling exchanges across Saudi Arabia, Kuwait, Oman and Bahrain, we succeeded in accomplishing a significant KPI we set for ourselves: boosting our customer satisfaction rate. While international exchanges did not improve our delivery time, we managed to make our customers happy by reserving products they liked and purchased in the sizes they wanted, ensured they continued to benefit from any deal or discount they purchased it with and if the price for that product went up, our customers weren’t obliged to pay the difference!

With at the doorstep exchanges, we went even further.  Not only did we further boost our customer satisfaction rate, we also managed to reduce overhead costs by having our couriers pick up the original item and drop off the exchange item in one trip. Our exchange delivery time went down from an average of 4.2 days to just 1.3 days in the UAE.


We’ve only been able to roll out doorstep within the UAE using our in-house carrier Last Mile. This is primarily because we had the capacity to train our courier agents on the swap process. Scaling this feature internationally would entail working with and training our courier partners to be able to conduct these swaps for us. This limitation prevents us from rolling this feature out internationally, but we would love to be able to work with our external courier partners to be able to do so!


*This article has been a joint effort between the Software Engineers and Product Managers who planned and changed the process: [Ala](http://tech.namshi.io/team/#Ala%20Hawash), our product manager Sakina Sagrwala,
[Ayham](http://tech.namshi.io/team/#Ayham%20Alzoubi), and our CTO [Alex](http://tech.namshi.io/team/#Alessandro%20Nadalin).*
