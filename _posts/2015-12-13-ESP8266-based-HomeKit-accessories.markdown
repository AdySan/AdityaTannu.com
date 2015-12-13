---
layout: post
title:  "ESP8266 based HomeKit accessories"
date:   2015-12-13 12:00:00
categories: blog post
author: "Ady"
---

Apple announced HomeKit a while ago. Quite a few HomeKit devices have been showing up at stores.

## HAP-NodeJS

These instructions are specific to RaspberryPi. I didn't want to keep a computer running all the time, so I chose to install HAP-NodeJS on a RaspberryPi. You might wanna install HAP-NodeJS on a computer too just as a backup option.

### Required libraries

- HAP-NodeJS requires some libraries, lets install them first

 ```
 sudo su
 apt-get install git-core libnss-mdns libavahi-compat-libdnssd-dev -y
 ```

### Node

You can get the link to the latest version of Node from [here](https://nodejs.org/en/download/). I meant to install Node v4.0 but installed v5.2 by mistake. It works anyway, at least so far.

 - Install node and some dependencies with the following commands

  ```
  wget https://nodejs.org/dist/v5.2.0/node-v5.2.0-linux-armv7l.tar.gz
  tar -xvf node-v5.2.0-linux-armv7l.tar.gz 
  cd node-v5.2.0-linux-armv7l
  sudo cp -R * /usr/local
  npm install -g node-gyp
  ```

## Install HAPNodeJS

```
git clone https://github.com/KhaosT/HAP-NodeJS.git
cd HAP-NodeJS/
npm install node-persist && npm install srp && npm install mdns --unsafe-perm
npm install debug
npm install ed25519 --unsafe-perm
npm install curve25519 --unsafe-perm
npm install mqtt --unsafe-perm

```

## Install an MQTT server [Mosca](https://github.com/mcollina/mosca)

`npm install mosca bunyan -g --unsafe-perm`
`mosca -v | bunyan`

## MQTT 
https://github.com/Imroy/pubsubclient


