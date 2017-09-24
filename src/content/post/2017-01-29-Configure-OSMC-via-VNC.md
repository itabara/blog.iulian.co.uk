---
title: "Configure OSMC via VNC"
date: 2017-01-29
categories:
- KODI
- Raspberry
tags:
- raspberry pi
- KODI
---

When running KODI (OSMC) on the Raspberry PI it's always handy to set in front of your desktop and adjust/install stuff.
So I end up setting up the vnc server on my Raspberry PI.

You'll need VNC Viewer to connect to your fresh Raspberry PI VNC server.

But first, let's connect to the raspberry pi instance via ssh (ssh osmc@raspberry_ip) and update it:

```Shell
sudo apt-get update
sudo apt-get upgrade
```

then you will need to install:

```Shell
sudo apt-get install libvncserver-dev
sudo apt-get install rbp-userland-dev-osmc
sudo apt-get install gcc wget https://github.com/hanzelpeter/dispmanx_vnc/archive/master.zip
unzip master.zip -d /home/osmc/
rm master.zip
```

We'll need to change the refresh rate so we're going to edit the main.c file before compiling it:
```Shell
sudo vim ./dispmanx_vnc-master/main.c
```

We're looking for
```
#define PICTURE_TIMEOUT(1.0/15.0)
```
to be replaced with:
```
#define PICTURE_TIMEOUT(1.0/25.0)
```
Then we'll compile:
```Shell
sudo chmod +x makeit
./makeit
```
Last step before connecting is:
```Shell
sudo modprobe uinput
sudo chmod 666 /dev/uinput
./dispman_vncserver
```
You need to keep the process running, then start your VNC viewer and use below url to control your raspberry PI.

```
raspberry_ip:0
```
Enjoy.
