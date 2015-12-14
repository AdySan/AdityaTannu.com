---
layout: post
title:  "ESP8266 based HomeKit accessories"
date:   2015-12-13 12:00:00
categories: blog post
author: "Ady"
---

# What is this about?

<p align="center">
<iframe align=“middle” width="420" height="315" src="http://www.youtube.com/embed/tGQj1bp5cOY" frameborder="0" allowfullscreen></iframe>
</p>

This video should give you an idea what we’re trying to achieve here. Of course HomeKit is a lot more powerful than just being able to switch a power outlet. 

# Background

Apple announced HomeKit a while ago. Lately, quite a few HomeKit devices have been showing up at stores. Despite being convinced about HomeKit's awesomeness I haven’t really bought a single official accessory yet. A few months ago, I stumbled upon [these](https://www.youtube.com/watch?v=jtk_X40radU) [videos](https://www.youtube.com/watch?v=VJTFwXa_Q0I). They opened up a whole new world of ESP8266 to me. In the next few weeks I got [this](https://www.instagram.com/p/6L7hbpEdT4/?taken-by=adysan) working with NodeMCU firmware running on the ESP8266. It did work but frankly I didn't find the Lua firmware very appealing. 

Since then I've been tinkering a lot with the [esp8266/Arduino](https://github.com/esp8266/Arduino) port. Last week I published this [Christmas Light project](https://github.com/AdySan/ChristmasLight). Again, it does work, but I'd rather not open a webpage every time I want to turn on the Christmas light. So we are back to HomeKit!

Involving HomeKit perhaps makes this HelloWorld kind of project a little too complicated. However, once you get it working, it will definitely be fun to explore.

# Topology

Let’s talk about that [video](https://www.youtube.com/watch?v=VJTFwXa_Q0I) a litte. Being completely new to ESP8266, Node, MQTT, etc. I was really cofused about how this stuff was working. Now that I have the same thing working, let me try and explain a little. Here are the components involved:

 - HAP-NodeJS running on the Raspberry Pi
 - MQTT Broker running on the Raspberry Pi
 - iPhone with the HomeKit accessory paired and configured
 - LED connected to an ESP8266 module
 - And of course a wireless router

Here’s how it works: On boot up HAP-NodeJS server connects to a MQTT broker (which is also happens to be running on the Raspberry Pi) so that it can publish messages on a certain topic. The ESP8266 module also connects to the MQTT broker and subscrbes to a certain topic. Telling Siri to turn on/off the light, triggers a certain accessory action on the HAP-NodeJS server. This action publishes a message "LightOn" on the topic "Light". The ESP8266, which has also subscribed to same topic, receives the message, parses it, and turns on the LED. 

# Let’s make it work

I’m assuming you have a working Raspberry Pi running Raspbian. If not go [here]({{ site.baseurl }}/blog/post/2015/12/13/Raspberry-Pi.html) and read how to get that working.

## HAP-NodeJS

These instructions are specific to RaspberryPi. I didn't want to keep a computer running all the time, so I chose to install HAP-NodeJS on a RaspberryPi. You might wanna [install](https://github.com/KhaosT/HAP-NodeJS/wiki) HAP-NodeJS on a computer too just as a backup option for testing.

### Install required libraries

- HAP-NodeJS requires some libraries, let’s install them first

```bash
sudo su
apt-get install git-core libnss-mdns libavahi-compat-libdnssd-dev -y
```

### Install Node

You can get the link to the latest version of Node from [here](https://nodejs.org/en/download/). I meant to install Node v4.0 but installed v5.2 by mistake. It works anyway, at least so far.

 - Install node and some dependencies with the following commands

   ```bash
  wget https://nodejs.org/dist/v5.2.0/node-v5.2.0-linux-armv7l.tar.gz
  tar -xvf node-v5.2.0-linux-armv7l.tar.gz 
  cd node-v5.2.0-linux-armv7l
  sudo cp -R * /usr/local
  npm install -g node-gyp
  ```

### Install HAPNodeJS

 ```bash
git clone https://github.com/KhaosT/HAP-NodeJS.git
cd HAP-NodeJS/
npm install node-persist && npm install srp && npm install mdns --unsafe-perm
npm install debug
npm install ed25519 --unsafe-perm
npm install curve25519 --unsafe-perm
npm install mqtt --unsafe-perm
```

Try running HAP-NodeJS `node BridgedCore.js`, should looks something like this

![HAP-NodeJS]({{ site.url }}/images/HAP-NodeJS.png)


## MQTT Broker

Now that HAP-NodeJS is running, next thing we need is an MQTT broker so that we can send and recieve commands to and from the ESP8266 modules. 

- Install an MQTT broker [Mosca](https://github.com/mcollina/mosca)

`npm install mosca bunyan -g --unsafe-perm`

- Run it

`mosca -v | bunyan`

If mosca runs without problems you should see something like this

![MOSCA]({{ site.url }}/images/mosca.png)


## ESP8266 Arduino sketch

You can find the source code for this part on GitHub at [https://github.com/AdySan/HomeKitLight](https://github.com/AdySan/HomeKitLight)

</br>
<p align="center">
<img src="{{ site.url }}/images/christmas_light.png" align="middle"alt="Christmas Light" height="600">
</p>

For the ESP8266 side, I’m using the [latest git version](https://github.com/esp8266/Arduino) of the Arduino port of ESP8266. For the Arduino IDE, I’m specifically using [arduino-PR-4107-BUILD-421](https://github.com/arduino/Arduino/pull/4107) because it supports OTA firmware updates.

All this sketch does is to connect to WiFi, connect to the MQTT broker and subscribe to the topic “AdyLight”. Once it receives a message on that topic, it parses it and toggles the relay GPIO accordingly.

### MQTT Library

The sketch is based on an [example](https://github.com/Imroy/pubsubclient/blob/master/examples/mqtt_subscriber/mqtt_subscriber.ino) in the [PubSubclient](https://github.com/Imroy/pubsubclient) MQTT library for ESP8266. 


## Making a new HomeKit accessory

You can refer to my fork of HAP-NodeJS [here](https://github.com/AdySan/HAP-NodeJS/blob/master/accessories/AdyLight_accessory.js)

- You just need to copy and existing accessory, in this case I copied `Light_accessory.js` to `AdyLight_accessory.js`

- Add connection to MQTT broker

```javascript
// MQTT Setup
var mqtt = require('mqtt');
var options = {
  port: 1883,
  host: '192.168.1.155',
  clientId: 'AdyPi_MQTT_Publisher'
};
var client = mqtt.connect(options);
```

- Publish on topic “AdyLight”

```javascript
  setPowerOn: function(on) { 
    console.log("Turning AdyLight %s!", on ? "on" : "off");

    if (on) {
			client.publish('AdyLight', 'on');
   	} else {
			client.publish('AdyLight','off');
   };
```

- If everything works well, you should see something like this on HAP-NodeJS and mosca

![HAPConnected]({{ site.url }}/images/HAPworking.png)

![mosca connected]({{ site.url }}/images/moscaconnected.png)


## HomeKit accessory pairing

Unfortunately Apple doesn’t provide a stock app for HomeKit. For adding and pairing the HomeKit accessory, you’ll need something. The most polished app I’ve seen is [Matthias Hochgatterer’s](https://twitter.com/brutella) [Home](http://selfcoded.com/home/) app.

If you can build your own app, try Apple’s official [HomeKit Catalog](https://developer.apple.com/library/ios/samplecode/HomeKitCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40015048) app. The maker of HAP-NodeJS, KhaosT, also has his [own version](https://github.com/KhaosT/HomeKit-Demo) of this app.

For Pairing use this pin code from the HAP-NodeJS source

```javascript
light.pincode = "031-45-154";
```

Here are some screenshots:


<p align="center">
<img src="{{ site.url }}/images/HM1.png" align="middle"alt="Circuit" style="heigh: 400px;"> <img src="{{ site.url }}/images/HM2.png" align="middle"alt="Circuit" style="heigh: 400px;"> <img src="{{ site.url }}/images/HM3.png" align="middle"alt="Circuit" style="heigh: 400px;"> <img src="{{ site.url }}/images/HM4.png" align="middle"alt="Circuit" height=“600"> <img src="{{ site.url }}/images/HM5.png" align="middle"alt="Circuit" height=“600"> <img src="{{ site.url }}/images/HM6.png" align="middle"alt="Circuit" height=“600"> <img src="{{ site.url }}/images/HM7.png" align="middle"alt="Circuit" height=“600"> <img src="{{ site.url }}/images/HM8.png" align="middle"alt="Circuit" height=“600”> <img src="{{ site.url }}/images/HM9.png" align="middle"alt="Circuit" height=“600"> <img src="{{ site.url }}/images/HM10.png" align="middle"alt="Circuit" height=“600"> <img src="{{ site.url }}/images/HM11.png" align="middle"alt="Circuit" height=“600"> <img src="{{ site.url }}/images/HM12.png" align="middle"alt="Circuit" height=“600"> <img src="{{ site.url }}/images/HM13.png" align="middle"alt="Circuit" height=“600"> <img src="{{ site.url }}/images/HM14.png" align="middle"alt="Circuit" height=“600">
</p>
</br>

</br>
<p align="center">
<img src="{{ site.url }}/images/AdyLightOn.PNG" align="middle"alt="Circuit" height="600"> <img src="{{ site.url }}/images/AdyLightOff.PNG" align="middle"alt="Circuit" height="600">
</p>


## Further reading

- [MQTT Lens](https://chrome.google.com/webstore/detail/mqttlens/hemojaaeigabkbcookmlgmdigohjobjm?hl=en): This Chrome app is super useful in testing MQTT

![MQTTLens]({{ site.url }}/images/MQTTLens.png)

- [ESP8266 Power-strip](http://www.penninkhof.com/2015/05/linking-the-esp8266-a-raspberry-pi-through-mqtt/)

- [Understanding MQTT better](http://thejackalofjavascript.com/getting-started-mqtt/)

- [Screen](http://raspi.tv/2012/using-screen-with-raspberry-pi-to-avoid-leaving-ssh-sessions-open): Since we’re gonna have two things running on the RPi, HAP-NodeJS and the MQTT broker, we need a way to monitor both these processes separately. Right now I’m just using two separate terminal windows to SSH into the RPi. Screen would be a [better option](https://coderwall.com/p/quflrg/run-a-script-on-startup-in-a-detached-screen-on-a-raspberry-pi), once you have both these processes [running at boot up](http://www.stuffaboutcode.com/2012/06/raspberry-pi-run-program-at-start-up.html).


