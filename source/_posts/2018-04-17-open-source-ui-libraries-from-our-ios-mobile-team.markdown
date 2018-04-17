---
layout: post
title: "Open Source UI libraries from our iOS Mobile team"
date: 2018-04-17 07:08
comments: true
author: Abdul Hannan
categories: 
  - ios
  - mobile
  - opensource
  - cocoapods
  - uikit
  - uitabbar
  - namshi-ios
---

Recently, the Namshi iOS app went through a UI overhaul which includes a new font, improved UX on a few screens and some attractive animations. Customers loved it, the team enjoyed working on it and, best of all, conversion rate increased.  To achieve this, we relied on a few open source libraries available through Cocoapods. 

Some of the available open source UI components are very well written and while working with these, you will get a lot of inspiration. I won’t hesitate to mention [SkyFloatinglabelTextField](https://github.com/Skyscanner/SkyFloatingLabelTextField/) from SkyScanner and [XLPagerTabStrip](https://github.com/xmartlabs/XLPagerTabStrip) here. Sometimes, the UI requirements are very specific and UI libraries will not support the particular use-case you have. While working on the UI improvement for Namshi iOS app, we faced the same situation where we had to modify an existing library to tweak its looks.
<!-- more -->
So it was a combination of inspiration and custom requirements that resulted in two awesome UI components which we recently published on Cocoapods. Let me Introduce these libraries separately below : 

# NMFloatLabelSearchField
![](https://d2mxuefqeaa7sj.cloudfront.net/s_ED3A3F43C1A3C37015D225DDB70A39423F77B009324EF3510BD4811463D17DED_1522079824203_CocoaPodsSwift-feature.png) <span style="margin-left: 15px">[www.cocoapods.org](http://cocoapods.org)</span>

![](https://d2mxuefqeaa7sj.cloudfront.net/s_ED3A3F43C1A3C37015D225DDB70A39423F77B009324EF3510BD4811463D17DED_1522078748825_github.png)  <span style="margin-left: 15px">[https://github.com/namshi/NMFloatLabelSearchField](https://github.com/namshi/NMFloatLabelSearchField)</span>

## Case Study : 

We had a requirement to implement UITextFields on which hints float up when the user starts to type; the border can also be highlighted based on different delegate callbacks and on validation errors. 

We found SkyFloatingLabelTextField which does that perfectly and supports RTL languages as well.  Here comes the challenge: we had a city suggestion field in the form which dynamically displays a suggestion list as user starts to type, and this feature is not supported in SkyFloatLabelTextField. So we started our search again and found one more library, SearchTextField. We went ahead with it and used both of them. 

Soon we realized that the UX of the screen is not appealing as five fields (name, country code, city code, phone number and address) are having floating-placeholders but the city field looks like a fish out of water here. We at Namshi are always eager to make the UX smooth and appealing for our customers, so we decided to join the two third-party libraries’ functionality and combine them for our city-search-field.

## Solution: 

In the beginning, we extended the functionality of SearchTextField and added the code from SkyFloatingLabelTextfield to achieve FloatingLabelSearchField functionality. It worked well but we realized that we are not properly getting the textField delegate callbacks (didEndEditing never worked). 
We looked into the open issues for SkyFloatingLabelTextField but there was none related to this. Then we looked for the open issues for SearchTextField and — voila! — we found an [open issue](https://github.com/apasccon/SearchTextField/issues/36) in the library. We changed our strategy; extended the functionality of SkyFloatingLabelTextfield and added the code for SearchTextField in the our code. We faced few bugs and me managed to fix those  and….. 
Yalla, it really worked!
Soon our app was in store with the awesome looking “Add New Address” screen with smooth user experience.

![](https://d2mxuefqeaa7sj.cloudfront.net/s_ED3A3F43C1A3C37015D225DDB70A39423F77B009324EF3510BD4811463D17DED_1522081067491_image_preview.png)

# NMAnimatedTabbarItem
![](https://d2mxuefqeaa7sj.cloudfront.net/s_ED3A3F43C1A3C37015D225DDB70A39423F77B009324EF3510BD4811463D17DED_1522079824203_CocoaPodsSwift-feature.png)  <span style="margin-left: 15px">[https://cocoapods.org/pods/NMAnimatedTabBarItem](https://cocoapods.org/pods/NMAnimatedTabBarItem)</span>

![](https://d2mxuefqeaa7sj.cloudfront.net/s_ED3A3F43C1A3C37015D225DDB70A39423F77B009324EF3510BD4811463D17DED_1522078748825_github.png)<span style="margin-left: 15px">[https://github.com/namshi/NMAnimatedTabbarItem](https://github.com/namshi/NMAnimatedTabbarItem)</span>

## Case Study : 

The tabbar used in Namshi app was pretty basic, it looked like the tabbar from Apple's built-in apps when iOS 7 was released. We realized that almost all the major apps are incorporating some animations on tab bar so it was the right time to spice up UITabbar used in Namshi app.

We first started with [Ramotion](https://github.com/Ramotion/animated-tab-bar) — this library is awesome! After playing with it for few hours, we realized that it has some deal breakers such as missing support  for RTL languages and has a problem putting tab items back into the correct position when you move to a screen which does not have a tabbar and try to come back to a screen which does. We forked the library, tried to solve the issues but gave up as, one after the other, new issues came up. 


## Solution: 

We started by digging deep into Ramotion and we got the basic idea how they are animating Tabbar items. We used the same approach and made the whole thing much more simpler. 

We created an open class NMAnimatedTabBarItem inherits from NSObject with a public method called animateTabBarItem.   

We have to pass 3 arguments to this method, tabBar(UITabBarController.tabBar), tabIndex (Selected tabItemIndex) and finally animationType(NMAnimationtype).   

NMAnimationtype could be:          

- Bounce
- Rotation
- Transition
- Frame 

For Bounce, Rotation and Transition tabbar item image required. For Frame animation we have to pass UIImage Array.

<span style="text-align: center; display: block">
![](https://d2mxuefqeaa7sj.cloudfront.net/s_ED3A3F43C1A3C37015D225DDB70A39423F77B009324EF3510BD4811463D17DED_1522583517426_ezgif.com-resize.gif)
</span>

<hr/>
# Some Useful Links for Creating Custom Pods
- [https://code.tutsplus.com/tutorials/creating-your-first-cocoapod--cms-24332](https://code.tutsplus.com/tutorials/creating-your-first-cocoapod--cms-24332)
- [https://guides.cocoapods.org/making/private-cocoapods.html](https://guides.cocoapods.org/making/private-cocoapods.html)
- [https://guides.cocoapods.org/making/specs-and-specs-repo.html](https://guides.cocoapods.org/making/specs-and-specs-repo.html)
- [https://medium.com/@shahabejaz/create-and-distribute-private-libraries-with-cocoapods-5b6507b57a03](https://medium.com/@shahabejaz/create-and-distribute-private-libraries-with-cocoapods-5b6507b57a03)
- [https://www.raywenderlich.com/99386/create-cocoapod-swift](https://www.raywenderlich.com/99386/create-cocoapod-swift)

     


