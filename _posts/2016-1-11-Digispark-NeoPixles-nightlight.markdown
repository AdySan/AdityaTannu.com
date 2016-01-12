---
layout: post
title:  "Ikea Solviden and NeoPixels night light"
date:   2016-1-11 12:00:00
categories: blog post
author: "Ady"
---

# What is this?

I made a quick night light using some things I found at home. It turned out rather beautiful. Here's a quick video:

<p align="center">
<blockquote class="twitter-video" lang="en"><p lang="en" dir="ltr">10 minute project: IKEA Solvinden and <a href="https://twitter.com/adafruit">@adafruit</a> NeoPixels night light <a href="https://t.co/ciTaGV4y8f">pic.twitter.com/ciTaGV4y8f</a></p>&mdash; Ady (@Ady) <a href="https://twitter.com/Ady/status/686778912186105856">January 12, 2016</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
</p>

# Features
- Auto On/Off at night: I wanted to keep the components to the minimum, so didnt even bother with a RTC. Just turns on for 12 hours and stays off for the next 12 hours!

- Slowly fading colors: Well if you're using NeoPixels, might as well make some animations.

- Very little soldering required.

# Components

- [IKEA SOLVIDEN](http://www.ikea.com/aa/en/catalog/products/00302026/): Picked up these 12 casings for $1.99 at IKEA.

- [Digispark](http://www.aliexpress.com/item/1pcs-CJMCU-Digispark-kickstarter-miniature-for-Arduino-usb-development-board/32432747996.html?spm=2114.01020208.3.10.aHiAuF&ws_ab_test=searchweb201556_1,searchweb201644_5_10001_10002_10005_10006_10003_10004_62_61,searchweb201560_8,searchweb1451318400_6150): A little over a dollar for a very convenient ATTiny85 board with integrates USB connector.

- [Adafruit Neopixels](https://www.adafruit.com/category/168): You can either get the original Adafruit ones or something [cheaper](http://www.aliexpress.com/store/all-wholesale-products/1051119.html) from AliExpress. Obviously I used the cheaper ones. 

- [Phone Charger](http://www.aliexpress.com/item/High-Quality-5V-2-1-1A-Dual-US-AC-Travel-USB-Wall-Charger-for-iPhone-Samsung/32319820235.html?spm=2114.01020208.3.11.ao7BlF&ws_ab_test=searchweb201556_1,searchweb201644_5_10001_10002_10005_10006_10003_10004_62,searchweb201560_8,searchweb1451318400_6150): You can use practically any phone charger you might find at home. I got some dirt cheap ones. All I wanted was something small with a USB port on it.

# Pictures

![night light 1]({{ site.url }}/images/nightlight1.jpeg)
</br>
![night light 2]({{ site.url }}/images/nightlight2.jpeg)
</br>
![night light 3]({{ site.url }}/images/nightlight3.jpeg)

# Future improvements

- If I used an ESP8266 instead of the digispark, I could make it a lot more intelligent. First thing I'd do as turn it On/Off with sunset and sunrise using the [NTPClient](https://github.com/FWeinb/NTPClient) library. Only thing is, I haven't seen any [ESP8266 board](https://twitter.com/Ady/status/686742995626934272) in this form factor yet. And using something like the NodeMCU would be too messy.

- Could add a PIR motion sensor easily.
