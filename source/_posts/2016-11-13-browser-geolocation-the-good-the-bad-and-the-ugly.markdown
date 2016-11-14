---
layout: post
title: "Browser geolocation: the good, the bad and the ugly"
date: 2016-11-13 18:37
comments: true
categories: [web api, geolocation, checkout]
author: Alessandro Nadalin
---

We're a little late to the party -- but we're here, amongst those who are
playing around with the [geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation/Using_geolocation)
provided by browsers.

Without further ado, let me get straight to our feedback on one the nicest web
APIs that have been standardized in recent times.

<!-- more -->

## Background

We've always been looking for ways to ease our checkout workflow, as a simpler
process usually means happier customers, and the ability to automagically
detect the device's location lets us take away from our customers the burden
of having to manually fill forms:

<div align="center">
  <video src="/videos/geolocation.webm" controls autoplay loop></video>
</div>

Now that you've seen it in action let's dig a little bit on what we found out
while implementing this little thing of beauty.

## The good

First off, let me start by saying that, luckily, browser support is
[widespread](http://caniuse.com/#feat=geolocation), as the only major browser
that is currently lacking support is Opera Mini (not a biggie).

Detecting the user's location is also quite simple:

``` js
var opts = {
  enableHighAccuracy: true,
  maximumAge        : 1000,
  timeout           : 1000
};

function onSuccess(position) {
  console.log(position.coords.latitude, position.coords.longitude)
}

function onError(err) {
  console.log(err)
}

navigator.geolocation.getCurrentPosition(onSuccess, onError, opts);
```

If this is the first time you're trying to access the device's position the
browser will show a popup to inform the user the website's trying to access the
device's location, so that he or she can accept or decline. The rest is history :)

**Takeaway**: this is going to sound like a typical 80/20 story -- it takes no
time to get most done, and a proportially long time to sort the wonky details
out. Regardless, the experience has been pretty positive.

## The bad

Detecting the device's location turns out pretty handy on desktop devices, as
you can safely assume they won't move around :) Phones and tablets, on the
other hand, will probably be used around so there's a good chance that the
customer might move away / be away from the destination.

The problem is that on desktop you don't really get a very accurate position as
the geolocation service the browser uses won't have any GPS triangulation
available, so you're left with heuristics based on [IP address mapping and previous
information about nearby networks](http://stackoverflow.com/questions/1668304/how-does-google-calculate-my-location-on-a-desktop).


**Takeaway**: we went mobile-first and decided to ignore desktop devices for now
(coming soon!).

In addition, error callbacks aren't as accurate as they are supposed to be: on
android, for example, if the user doesn't have the GPS enabled, you might get a
very cryptic error such as "*User denied geolocation*" -- which isn't really
what's happening.

**Takeway**: don't really rely on the error callbacks to distinguish between
errors, as they're not 100% reliable across all platforms. Consider any error as
a general failure, indicating that something might have gone wrong:

* the user didn't accept the geolocation request
* the geolocation request failed for some reason
* the GPS wasn't turned on
* apocalypse just happened :)

Last but not least, we were very surprised that geolocation depended on the GPS
being manually turned on, at least on Android. I don't know how
many android users go around with their GPS turned on but I bet it's a pretty
small percentage considering how it affects battery usage. We thought the
browser could temporarily turn the GPS on on its own, but that isn't the case. On
iOS, for some reason, it seems the percentage of users having *location services*
enabled is quite higher, thus the impact isn't as high.

**Takeaway**: be prepared to help users figure out what's wrong -- give them
clear instructions on what steps are required to successfully geolocate them:

* location services / GPS must be on
* internet access (well...)
* accept the geolocation request

## The ugly

Oh boy, we didn't see this coming.

Follow me on this simple workflow on an android phone:

* trigger a geolocation request, but with the GPS off
* request fails for obvious reasons
* enable GPS
* trigger a new geolocation request

What in the world would you expect to happen?

Surprise, it doesn't work. And guess what, if you refresh the page an trigger
the request again everything's good -- so somehow chrome isn't really able to
"recover" from a failed request until the page is refreshed. Go figure.

Of course, triggering a full page reload on our checkout is out of question, as
that distracts the user away and delays the checkout process, so we looked around
for alternative solutions and thought of giving iframes a try -- surprisingly,
it worked. Go figure.

The idea is very simple -- once the user clicks on the "*Detect my location*"
button we load an invisible iframe that triggers the geolocation request:

``` js
getCurrentPosition = function(){
  return new Promise((resolve, reject) => {
    var ifr = document.createElement('iframe');
    ifr.style.opacity = '0';
    ifr.style.pointerEvents = 'none';
    ifr.src = window.location.origin + '/geo.html';

    document.body.appendChild(ifr);

    ifr.contentWindow.addEventListener('message', function(message){
      message = JSON.parse(message.data);
      document.body.removeChild(ifr);

      if(message.type === 'success'){
        resolve(message.data);
      } else {
        reject(message.data);
      }
    });
  ]})
};
```

(the above code got trimmed for the sake of brevity)

As you might have figured, we listen for a message from the iframe, which is
responsible for getting the users' location and sending it to us:

``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Geolocation</title>
</head>
<body>
  <script type="text/javascript">
    var triggerGeolocationRequest = function(){
      var options = {
        enableHighAccuracy: true,
        timeout: 1000,
        maximumAge: 1000
      };

      var result;

      window.navigator.geolocation.getCurrentPosition(function(position){
        var result = {
          type: 'success',
          data: {lat: position.coords.latitude, lng: position.coords.longitude}
        };

        window.postMessage(JSON.stringify(result), window.location.origin);
      }, null, options)
    };

    window.addEventListener('load', triggerGeolocationRequest);
  </script>
</body>
</html>
```

(the above code got trimmed for the sake of brevity)

And that does the job: the browser sees the iframe as a brand new page, thus
is able to overcome this wonky issue. Again, go figure.

**Takeaway**: iframes, rescuing lazy web developers since 1997.

## All in all...

Our feedback is generally positive, as customers have started to use it from day
1 at a good rate (~10% of our mobile checkouts is "geolocated"). There are a few
quirks here and there but if you survived IE6 then this is really going to feel
like a piece of cake.

A big "thanks" goes to my partners in crime [Mohamed](/team/#Mohamed Amin), [Gabriel](/team/#Gabriel Izebhigie), [Shidhin](/team/#Shidhin CR), [Razan](/team/#Razan Bilwani) and
[Yomna](/team/#Yomna Sabry), the real masterminds behind this new feature :)

Au revoir!
