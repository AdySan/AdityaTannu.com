---
layout: post
title:  "WeatherBug: ESP8266 WeatherStation with IFTTT and HomeKit support"
date:   2016-1-1 12:00:00
categories: blog post
author: "Ady"
---

# What is this about?

I assume you're familiar with Daniel Eichhorn's [ESP8266 Weather Station](http://blog.squix.ch/2015/12/esp8266-weather-station-measuring.html). I modified it to do a little more.

</br>
<p align="center">
<img src="{{ site.url }}/images/WeMosD1mini.jpg" align="middle"alt="WeMos D1 Mini" height="600"> <img src="{{ site.url }}/images/WeatherBug.jpg" align="middle"alt="WeatherBug" height="600">
</p>

</br>
<p align="center">
<img src="{{ site.url }}/images/TooCold.PNG" align="middle"alt="TooCold" height="600"> <img src="{{ site.url }}/images/TooHot.PNG" align="middle"alt="TooHot" height="600"> <img src="{{ site.url }}/images/CurrentTemp.PNG" align="middle"alt="SiriTemperature" height="600">
</p>


## Hardware changes

- Used a WeMos D1 mini with the DHT shield

## Software changes

- Added IFTTT support for getting notifications when indoor temperature is too high or low.
- Publish indoor temperature over MQTT, so it is accessible via HomeKit with Siri.
- Made it work with Arduino 1.6.7 by using NTPclient library.

# Background

As you may [know](http://adyshan.com/baby/2015/10/08/its-a-girl.html), we're having a baby soon! We've been busy preparing the baby's nursery for the last few months. Here in Southern California, the weather is rather consistent, but it does get a little uncomfortable sometimes. When I [got](https://twitter.com/Ady/status/679855803046572032) my WeMos D1 Mini two weeks ago, I was thinking about putting it to some good use. A temperature sensor for the nursery seemed like a [great idea](https://www.instagram.com/p/-aipwMEdbf/?taken-by=adysan). Rather than just logging the temperature somehwere, I wanted to be notified when the nursery was too cold or hot. Also, it would be nice if the temperature was accessible on all family members' iPhones via HomeKit. And when I saw Daniel Eichhorn's [ESP8266 Weather Station](http://blog.squix.ch/2015/12/esp8266-weather-station-measuring.html), I decided to merge all these into one thing, the WeatherBug. Well, the original idea was to have it hidden somewhere under the crib, hence a 'bug'.

# Steps to reproduce

## WeatherStation

First thing I would recommend is to get the original WeatherStation code working by following the instructions [here](https://github.com/squix78/esp8266-weather-station).

## IFTTT

- Go enable the IFTTT Maker channel here: https://ifttt.com/maker
- Get your API key.
- Paste the key [here](https://github.com/AdySan/WeatherBug/blob/master/WeatherBug.ino#L86) in my WeatherBug code.

## HomeKit

- If you're wondering how HomeKit fits into the picture here, first go read [this]({{ site.baseurl }}/blog/post/2015/12/13/ESP8266-based-HomeKit-accessories.html) and try to get that working. I've written a fairly detailed guide to make generic ESP8266 based HomeKit accessories. 
- If you notice, the accessories [folder](https://github.com/AdySan/HAP-NodeJS/tree/master/accessories) also includes a Temperature Sensor. All we need to do is to provide it the temperature value by publishing it over MQTT.
- You can see a working example of the accessory [here](https://github.com/AdySan/HAP-NodeJS/blob/master/accessories/NurseryTemperatureSensor_accessory.js) in my fork on HAP-NodeJS.

## Known issues

- The UI does freeze when updating sometimes.
- I think I fixed the [hysterisis problem](https://twitter.com/Ady/status/681942912817467392), not sure yet.

## Future improvements

- I don't want the entire UI to scroll. Since this is a temperature sensor first, I want the indoor temperature to be static on the screen, the rest of the screen and scroll through different items like forecast, outside temperature, etc.
- Try to get Helvetica on this screen. I really don't like Arial..