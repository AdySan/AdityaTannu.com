---
layout: post
title:  "ESP8266 Witty Cloud Board Demo"
date:   2016-1-31 12:00:00
categories: blog post
author: "Ady"
---

<p align="center">
<img src="https://cloud.githubusercontent.com/assets/744810/12700003/5ac3cf0a-c786-11e5-93c3-5b146e50b894.jpg" alt="Witty Cloud Board" style="width:304px;">
</p>

This is a followup post to last week's [post]({{ site.baseurl }}/blog/post/2016/01/24/ESP8266-Websockets-demo-using-NeoPixels.html) about using websockets to control NeoPixels. I've been slowly teaching myself HTML and JavaScript and in the process I came up with this simple project to demo all the peripherals on this new [Witty Cloud board](http://www.aliexpress.com/item/ESP8266-serial-WIFI-Witty-cloud-Development-Board-ESP-12F-module/32516750994.html). It had a bunch or peripherals like RGB LED, LDR and a user button. The interface is based on this awesome [ESP8266 Analog Broadcast Television Interface](https://github.com/cnlohr/channel3) you might have seen recently. 

# Here's a demo video

Just go to http://Witty.local/ on any device with a browser.

<p align="center">
<iframe width="560" height="315" src="https://www.youtube.com/embed/rkWhuOZr1VU" frameborder="0" allowfullscreen></iframe>
</p>

# Features

- Websocket connection with ESP8266 board
- LDR, RGB LED and button
- HTML page hosted from ESP8266 SPIFFS

# Source code

## [HTML Page](https://github.com/AdySan/WittyCloudTest/tree/master/WittyCloudTest/data)

This HTML page needs the whole `\data` folder to be uploaded to the SPIFFS. Just keep it in the same folder as your sketch as in this repo and use Tools > ESP8266 Sketch Data Upload, which will copy all the files to the SPIFFS, and these will be accessible to your sketch. If you do not see this menu option, try installing the [SPIFFS tool](https://github.com/esp8266/Arduino/blob/master/doc/filesystem.md#uploading-files-to-file-system). 

You'll need to modify [this](https://github.com/AdySan/WittyCloudTest/blob/master/WittyCloudTest/data/menuinterface.js#L7) line in `menuinterface.js` to match you'r boards IP addres. Ideally this should work with `var connection = new WebSocket('ws://'+location.hostname+':81/', ['arduino']);` but today I didnt have any luck getting it to work. You can try, might work in your setup.

## [Arduino Sketch](https://github.com/AdySan/WittyCloudTest/blob/master/WittyCloudTest/WittyCloudTest.ino)

This sketch is based on two examples [FSBrowser.ino](https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WebServer/examples/FSBrowser/FSBrowser.ino) and [WebSocketServer.ino](https://github.com/Links2004/arduinoWebSockets/blob/master/examples/WebSocketServer/WebSocketServer.ino). It's basically running a HTTP server and Websocket server. The HTML/JS files are loaded from the SPIFFS. When it recieves certain commands on the websocket connection, it responds with a specific message. So this websocket connection is essentially working in a ping-pong fashion and it should work as fast as possible. I've seen it work consistently at around 230Hz, your mileage may vary. When testing at home with my Apple Airport Extreme, for some reason the connection keeps dropping every 30 sec to 1 min. To make it stable I had to add [such](https://github.com/AdySan/WittyCloudTest/blob/master/WittyCloudTest/WittyCloudTest.ino#L65) delay statements to throttle it down. I've been playing with WireShark to analyse this problem with [Markus's](https://github.com/Links2004) help, if you have any idea let me know! Basically here's what happens:

```
[0] Disconnected!
[1] Disconnected!
[1] Connected from 0.0.0.0 url: 
[2] Disconnected!
[2] Connected from 0.0.0.0 url: 
[3] Disconnected!
[3] Connected from 0.0.0.0 url: 
[0] Connected from 192.168.1.10 url: /
[0] Got message: ping
LmacRxBlk:1
LmacRxBlk:1
LmacRxBlk:1
[0] Disconnected!
[1] Disconnected!
[1] Connected from 0.0.0.0 url: 
[2] Disconnected!
[2] Connected from 0.0.0.0 url: 
[3] Disconnected!
[3] Connected from 0.0.0.0 url:
```

The connections drops, the browser tries to reconnect but the IP is strangely 0.0.0.0. Becauce of these multiple connection requests, the ESP8266 throws a `LmacRxBlk:1`, saying its TCP buffer is full. I got it to work on a non-Apple router by [disabling](https://github.com/AdySan/WittyCloudTest/blob/master/WittyCloudTest/WittyCloudTest.ino#L343) Wifi sleep mode. But with an Apple Airport Extreme, the only thing that seems to work is throttling it down. I know for sure that this is not an ESP8266 issue because of [this](https://www.youtube.com/watch?v=8ISbmQTbjDI) video. Let me know if you have any ideas!

# Screenshots

- HTML page rendered on desktop browser

<p align="center">
<img src="{{ site.url }}/images/Websockets3.png" align="middle"alt=Fountain>
</p>

# Components

- [Witty Cloud board](http://www.aliexpress.com/item/ESP8266-serial-WIFI-Witty-cloud-Development-Board-ESP-12F-module/32516750994.html)

# Toolchain

- Arduino IDE 1.6.7
- ESP8266/Arduino Core (latest form git, but the 2.0.0.0 release should work too)
- [arduinoWebSockets library](https://github.com/Links2004/arduinoWebSockets)

# Known Issues

 - As explained above, the connection drops every now and then
 - Websocket connection doesn't work with mDNS or `location.hostname` for some reason
 - Cannot handle multiple clients

# Future improvements 

- Try to use AngularJS and Bootstrap to make the pge look better as suggested by [@nikil511](https://twitter.com/nikil511). 
- Not use jQuery if not absolutely necessary
