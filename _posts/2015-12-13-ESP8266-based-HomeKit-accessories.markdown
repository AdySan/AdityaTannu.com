---
layout: post
title:  "ESP8266 based HomeKit accessories"
date:   2015-12-13 12:00:00
categories: blog post
author: "Ady"
---

# What is this about?

This video should give you an idea what we’re trying to achieve here. Of course HomeKit is a lot more powerful than just being able to switch a power outlet. 

# Background

Apple announced HomeKit a while ago. Lately, quite a few HomeKit devices have been showing up at stores. Despite being convinced about HomeKit's awesomeness I haven’t really bought a single official accessory yet. A few months ago, I stumbled upon [these](https://www.youtube.com/watch?v=jtk_X40radU) [videos](https://www.youtube.com/watch?v=VJTFwXa_Q0I). They opened up a whole new world of ESP8266 to me. In the next few weeks I got [this](https://www.instagram.com/p/6L7hbpEdT4/?taken-by=adysan) working with NodeMCU firmware running on the ESP8266. It did work but frankly I didn't find the Lua firmware very appealing. 

Since then I've been tinkering a lot with the [esp8266/Arduino](https://github.com/esp8266/Arduino) port. Last week I published this [Christmas Light project](https://github.com/AdySan/ChristmasLight). Again, it does work, but I'd rather not open a webpage every time I want to turn on the Christmas light. So we are back to HomeKit!

Involving HomeKit perhaps makes this HelloWorld kind of project a little too complicated. However, once you get it working, it will definitely be fun to explore.

# Topology

Lets talk about that [video](https://www.youtube.com/watch?v=VJTFwXa_Q0I) a litte. Being completely new to ESP8266, Node, MQTT, etc. I was really cofused about how this stuff was working. Now that I have the same thing working, let me try and explain a little. Here are the components involved:

 - HAP-NodeJS running on the Raspberry Pi
 - MQTT Broker running on the Raspberry Pi
 - iPhone with the HomeKit accessory paired and configured
 - LED connected to an ESP8266 module
 - And of course a wireless router

Here’s how it works: On boot up HAP-NodeJS server connects to a MQTT broker (which is also happens to be running on the Raspberry Pi) so that it can publish messages on a certain topic. The ESP8266 module also connects to the MQTT broker and subscrbes to a certain topic. Telling Siri to turn on/off the light, triggers a certain accessory action on the HAP-NodeJS server. This action publishes a message "LightOn" on the topic "Light". The ESP8266, which has also subscribed to same topic, receives the message, parses it, and turns on the LED. 

# Let’s make it work

## [HAP-NodeJS](https://github.com/KhaosT/HAP-NodeJS)

These instructions are specific to RaspberryPi. I didn't want to keep a computer running all the time, so I chose to install HAP-NodeJS on a RaspberryPi. You might wanna [install](https://github.com/KhaosT/HAP-NodeJS/wiki) HAP-NodeJS on a computer too just as a backup option.

### Required libraries

- HAP-NodeJS requires some libraries, lets install them first

 ```Shell
 sudo su
 apt-get install git-core libnss-mdns libavahi-compat-libdnssd-dev -y
 ```

### Node

You can get the link to the latest version of Node from [here](https://nodejs.org/en/download/). I meant to install Node v4.0 but installed v5.2 by mistake. It works anyway, at least so far.

 - Install node and some dependencies with the following commands

   ```Shell
  wget https://nodejs.org/dist/v5.2.0/node-v5.2.0-linux-armv7l.tar.gz
  tar -xvf node-v5.2.0-linux-armv7l.tar.gz 
  cd node-v5.2.0-linux-armv7l
  sudo cp -R * /usr/local
  npm install -g node-gyp
  ```

## Install HAPNodeJS

 ```Shell
git clone https://github.com/KhaosT/HAP-NodeJS.git
cd HAP-NodeJS/
npm install node-persist && npm install srp && npm install mdns --unsafe-perm
npm install debug
npm install ed25519 --unsafe-perm
npm install curve25519 --unsafe-perm
npm install mqtt --unsafe-perm

```

## MQTT Broker

Install an MQTT server [Mosca](https://github.com/mcollina/mosca)

`npm install mosca bunyan -g --unsafe-perm`
`mosca -v | bunyan`


## ESP8266 Arduino

![Circuit]()

### MQTT Library
https://github.com/Imroy/pubsubclient


## HomeKit App



## Further reading

### MQTT Lens

### 
