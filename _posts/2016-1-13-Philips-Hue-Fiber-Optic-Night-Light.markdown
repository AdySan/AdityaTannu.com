---
layout: post
title:  "Philips Hue Fiber Optic Night Light"
date:   2016-1-13 12:00:00
categories: blog post
author: "Ady"
---

After making a [Philips Hue clone](https://twitter.com/Ady/status/679146825136259073), I realized that it would look really nice with a Fiber Optic light. I found a cheap [$3 light](http://www.aliexpress.com/item/Romantic-Color-Changing-Multicolor-Colorful-Fiber-Optic-Night-Light-Bar-Hotel-Decor-Decoration-Lamp/32531102478.html) on AliExpress. It arrived yesterday and seems to have rather decent build quality, for $3 that is. Here's how mine looks.

<p align="center">
<img src="{{ site.url }}/images/fountainlight2.JPG" align="middle"alt=Fountain>
</p>

Next thing I did was of course a teardown...

- Looks like there's three LEDs in there, one each of R-G-B.

<p align="center">
<img src="{{ site.url }}/images/fountainlight3.JPG" align="middle"alt=Fountain>
</p>

- Here's how the optic fibers look like.

<p align="center">
<img src="{{ site.url }}/images/fountainlight4.JPG" align="middle"alt=Fountain>
</p>

- Opening up the base revels a rather simple circuit, board, button and battery, thats it.

<p align="center">
<img src="{{ site.url }}/images/fountainlight5.JPG" align="middle"alt=Fountain>
</p>

- Looking under the board, there's not much.

<p align="center">
<img src="{{ site.url }}/images/fountainlight6.JPG" align="middle"alt=Fountain>
</p>

- Hmm, I think I can make this better.

<p align="center">
<img src="{{ site.url }}/images/fountainlight7.JPG" align="middle"alt=Fountain>
</p>

- Oh look, a NodeMCU board fits in perfectly in the battery compartment!

<p align="center">
<img src="{{ site.url }}/images/fountainlight8.JPG" align="middle"alt=Fountain>
</p>

- Let's remove the LEDs so I can put some NeoPixels on there.

<p align="center">
<img src="{{ site.url }}/images/fountainlight9.JPG" align="middle"alt=Fountain>
</p>

- I just need this board as a base for the NeoPixels

<p align="center">
<img src="{{ site.url }}/images/fountainlight10.JPG" align="middle"alt=Fountain>
</p>

- Here's my shoddy soldering job, I hope it works.

<p align="center">
<img src="{{ site.url }}/images/fountainlight11.JPG" align="middle"alt=Fountain>
</p>

- Some electrical tape to prevent shorting

<p align="center">
<img src="{{ site.url }}/images/fountainlight12.JPG" align="middle"alt=Fountain>
</p>

- Everything seems to work! Let's put it all back in.

<p align="center">
<img src="{{ site.url }}/images/fountainlight13.JPG" align="middle"alt=Fountain>
</p>

- Here's the final result 

<p align="center">
<img src="{{ site.url }}/images/fountainlight1.JPG" align="middle"alt=Fountain>
</p>

- Let's see it in action!

<p align="center">
<iframe width="560" height="315" src="https://www.youtube.com/embed/xbOEbz2knmQ" frameborder="0" allowfullscreen></iframe>
</p>

# Features

- Turn On/Off and set any color with Siri. 

- Set brightness with Siri.  

- NeoPixel animations possible too, but not the best fit for hte HomeKit use case.

- Powered with a phone charger using a micro USB cable.

- OTA programmabe, in case something breaks, the micro USB cable works too.

# Components

- [Fibert optic light](http://www.aliexpress.com/item/Romantic-Color-Changing-Multicolor-Colorful-Fiber-Optic-Night-Light-Bar-Hotel-Decor-Decoration-Lamp/32531102478.html): There are cheaper ones ~$1-2, but I needed some space to put in the ESP8266 board and NeoPixels.

- [Adafruit Neopixels](https://www.adafruit.com/category/168): You can either get the original Adafruit ones or something [cheaper](http://www.aliexpress.com/store/all-wholesale-products/1051119.html) from AliExpress.

- [NodeMCU V1.0 board](http://www.aliexpress.com/item/New-Wireless-module-NodeMcu-Lua-WIFI-Internet-of-Things-development-board-based-ESP8266-with-pcb-Antenna/32299982691.html): Even the Wemos D1 Mini will do.

- [Micro USB Cable](http://www.aliexpress.com/item/New-3ft-1M-2M-3M-Durable-Braided-Micro-USB-Cable-Charger-Data-Sync-Cable-Cord-For/32409663663.html): These braided ones are very flexible.

- [Phone Charger](http://www.aliexpress.com/item/High-Quality-5V-2-1-1A-Dual-US-AC-Travel-USB-Wall-Charger-for-iPhone-Samsung/32319820235.html?spm=2114.01020208.3.11.ao7BlF&ws_ab_test=searchweb201556_1,searchweb201644_5_10001_10002_10005_10006_10003_10004_62,searchweb201560_8,searchweb1451318400_6150): You can use practically any phone charger you might find at home. I got some dirt cheap ones. All I wanted was something small with a USB port on it.


# Source code

The ESP8266/Arduino side of things is rather easy here. Setting up the HomeKit part using HAP-NodeJS needs some work. I have a detailed post on how to do that [here]({{ site.baseurl }}/blog/post/2015/12/13/ESP8266-based-HomeKit-accessories.html). If you're not interested in in HomeKit, you can also host a html page on the ESP8266 and control the NeoPixel colors using websockets.

## [HAP-NodeJS accessory](https://github.com/AdySan/HAP-NodeJS/blob/master/accessories/Fountain_accessory.js)

Whenever this accessory recieves either On/Off or Hue, Saturation and Brightness values from an iOS device, it publishes them on MQTT topics `FountainLight`, `FountainLightHue`, `FountainLightSaturation` and `FountainLightBrightness`. I originally wanted to name this 'Fountain' but Siri kept confusing Fountain with Phone, so I had to go with something [discreet](https://github.com/AdySan/HAP-NodeJS/blob/master/accessories/Fountain_accessory.js#L87).

## [Arduino Sketch](https://gist.github.com/AdySan/bd23f7be5ca5a6a4563f)

On bootup the ESP8266 connects to Wifi and subscribes to MQTT topics `FountainLight`, `FountainLightHue`, `FountainLightSaturation` and `FountainLightBrightness`. Whenever it receives a value on these topics, it sets the NeoPixel strip with those values.  

# Notes

- HomeKit treats lights as single color lamps. However, NeoPixels can do more. It would be nice to sneak in some color fading. It's technically very easy to do but Siri (HomeKit API) doesn't really let you specify anything other than Hue, Brightness and Saturation. When you say Turn on the light, HomeKit just sends an 'on' command. We could interpret this command as a trigger to start a fading animation. However, since we're constantly listening to MQTT messages over WiFi, it would be better to use the DMA controlled [esp8266_ws2812_i2s](https://github.com/JoDaNl/esp8266_ws2812_i2s) library for this. For some reason this didnt work today for me, will give it a try some other day. Note that the NeoPixels data pin needs to be connected to GPIO03 or RX pin of the ESP8266 for this library.

- If you do not prefer to use Siri, you can also use a HomeKit app. The best so far I've seen is the [Home](http://selfcoded.com/home/) app. If you're a cheapo like, jsut use Apple [free example](https://developer.apple.com/library/ios/samplecode/HomeKitCatalog/Introduction/Intro.html). 