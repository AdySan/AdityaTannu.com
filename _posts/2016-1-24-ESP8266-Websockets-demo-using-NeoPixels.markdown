---
layout: post
title:  "ESP8266 Websockets demo using NeoPixels"
date:   2016-1-24 12:00:00
categories: blog post
author: "Ady"
---

This post is inspired by a reddit [post](https://www.reddit.com/r/esp8266/comments/42dvxd/reading_serial_data_on_phonelaptop/) on the esp8266 subreddit. The awesome [ESP8266 Websockets library](https://github.com/Links2004/arduinoWebSockets) has been around for a while, but I haven't really been able to use it any projects yet, apart from [ESPSocket](https://github.com/AdySan/ESPSocket), but that probably was more of a learning exercise for me. This simple project is (hopefully) first of many such samples of how this library can be used in some fun ways.  

# Here's a demo video

Just go to http://WebsocketsTest.local/ on any device with a browser to control the NeoPixels

<p align="center">
<iframe width="560" height="315" src="https://www.youtube.com/embed/GSVDY9_Wci0" frameborder="0" allowfullscreen></iframe>
</p>

# Features

- Streams data from browser to ESP8266 using a websocket connection
- Notification on succesful Websocket connection
- Change R, G, B, individual pixel, On/Off
- HTML page stored on ESP8266 SPIFFS
- Update HTML page via browser

# Source code

## [HTML Page](https://github.com/AdySan/ESP8266_WebSockets_NeoPixels/tree/master/data)

This HTML page needs the whole `\data` folder to be uploaded to the SPIFFS. Just keep it in the same folder as your sketch as in this repo and use Tools > ESP8266 Sketch Data Upload, which will copy all the files to the SPIFFS, and these will be accessible to your sketch.

If you want to keep these files on your computer (for testing) that's fine too, just change `var connection = new WebSocket('ws://'+location.hostname+':81/', ['arduino']);
` to `var connection = new WebSocket('ws://websocketstest.local:81/', ['arduino']);`

## [Arduino Sketch](https://github.com/AdySan/ESP8266_WebSockets_NeoPixels/blob/master/ESP8266_WebSockets_NeoPixels.ino)

This sketch seems a little too long at first glance because it is based on two examples [FSBrowser.ino](https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WebServer/examples/FSBrowser/FSBrowser.ino) and [WebSocketServer.ino](https://github.com/Links2004/arduinoWebSockets/blob/master/examples/WebSocketServer/WebSocketServer.ino). It's basically running a HTTP server and Websocket server. The HTML/JS files are loaded from the SPIFFS. When it recieves certain values on the websocket connection, it goes and updates the NeoPixels. I'm using the UARTDriven branch of the [NeoPixelBus](https://github.com/Makuna/NeoPixelBus/tree/UartDriven) library to avoid and long delays as I plan to do more animations in the future. The Adafruit libary might work fine too.

# Screenshots

- HTML page rendered on desktop browser

<p align="center">
<img src="{{ site.url }}/images/Websockets1.png" align="middle"alt=Fountain>
</p>

- Updating HTML page is super easy with the http://WebsocketsTest.local/edit interface

<p align="center">
<img src="{{ site.url }}/images/Websockets2.png" align="middle"alt=Fountain>
</p>

# Components

- [Adafruit Neopixels](https://www.adafruit.com/category/168): You can either get the original Adafruit ones or something [cheaper](http://www.aliexpress.com/store/all-wholesale-products/1051119.html) from AliExpress.

- [NodeMCU V1.0 board](http://www.aliexpress.com/item/New-Wireless-module-NodeMcu-Lua-WIFI-Internet-of-Things-development-board-based-ESP8266-with-pcb-Antenna/32299982691.html): Even the Wemos D1 Mini will do.

# Toolchain

- Arduino IDE 1.6.7
- ESP8266/Arduino Core (latest form git, but the 2.0.0.0 release should work too)
- [UARTDriven branch of NeoPixelBus library](https://github.com/Makuna/NeoPixelBus/tree/UartDriven)
- [arduinoWebSockets library](https://github.com/Links2004/arduinoWebSockets)

# Connections

NeoPixels Power and Gnd goes to Vin (5V) and Gnd of NodeMCU board. The Data pin goes to D4 (TXD1), this cannot be changed if you want to use the [NeoPixelBus library](https://github.com/Makuna/NeoPixelBus/tree/UartDriven) (UART Driven branch).

# Future improvements 

- Use the iPhone Gyro instead of sliders like I did [here](https://www.instagram.com/p/-ii54rEdew/)
- Change RGB to HSL, I don't really find RGB very intuitive. 
- Make the HTML page render better, load faster, not scroll, etc. on iOS Safari 
- Add proper margins to the HTML page, sorry I'm a total noob at web stuff
- Not use jQuery if not absolutely necessary
- Make websocket messages print on same line in Serail terminal to avoid scrolling
- Could add NeoPixel animations, but that's nothing to do with WebSockets really
- Use NeoPixel strip as a OTA progress bar for firmware updates. I know it's possible becasue of [this](https://twitter.com/Ady/status/683342658916384768).