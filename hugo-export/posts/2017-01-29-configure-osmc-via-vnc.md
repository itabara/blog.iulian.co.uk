---
title: Configure OSMC via VNC
author: Iulian
type: post
date: 2017-01-29T11:36:51+00:00
url: /2017/01/configure-osmc-via-vnc/
categories:
  - Raspberry_PI
tags:
  - Raspberry PI
  - VNC

---
When running KODI (OSMC) on the Raspberry PI it&#8217;s always handy to set in front of your desktop and adjust/install stuff.
  
So I end up setting up the vnc server on my Raspberry PI.

You&#8217;ll need [VNC Viewer][1] to connect to your fresh Raspberry PI VNC server.

But first, let&#8217;s connect to the raspberry pi instance via ssh (**ssh osmc@raspberry_ip**) and update it:

<pre class="lang:sh decode:true">sudo apt-get update
sudo apt-get upgrade</pre>

then you will need to install:

<pre class="lang:sh decode:true">sudo apt-get install libvncserver-dev
sudo apt-get install rbp-userland-dev-osmc
sudo apt-get install gcc
wget https://github.com/hanzelpeter/dispmanx_vnc/archive/master.zip
unzip master.zip -d /home/osmc/
rm master.zip</pre>

We&#8217;ll need to change the refresh rate so we&#8217;re going to edit the **main.c** file before compiling it:

<pre class="lang:sh decode:true ">sudo vim  ./dispmanx_vnc-master/main.c</pre>

We&#8217;re looking for

<pre class="lang:c decode:true ">#define PICTURE_TIMEOUT(1.0/15.0)</pre>

to be replaced with:

<pre class="lang:c decode:true">#define PICTURE_TIMEOUT(1.0/25.0)</pre>

Then we&#8217;ll compile:

<pre class="lang:sh decode:true ">sudo chmod +x makeit
./makeit</pre>

Last step before connecting is:

<pre class="lang:c decode:true ">sudo modprobe uinput
sudo chmod 666 /dev/uinput
./dispman_vncserver</pre>

You need to keep the process running:<a href="https://www.iuliantabara.com/2017/01/configure-osmc-via-vnc/screen-shot-2017-01-29-at-13-31-31/" rel="attachment wp-att-1202"><img class="aligncenter size-full wp-image-1202" src="https://www.iuliantabara.com/wp-content/uploads/2017/01/Screen-Shot-2017-01-29-at-13.31.31.png" alt="" width="752" height="597" srcset="https://www.iuliantabara.com/wp-content/uploads/2017/01/Screen-Shot-2017-01-29-at-13.31.31.png 752w, https://www.iuliantabara.com/wp-content/uploads/2017/01/Screen-Shot-2017-01-29-at-13.31.31-300x238.png 300w, https://www.iuliantabara.com/wp-content/uploads/2017/01/Screen-Shot-2017-01-29-at-13.31.31-378x300.png 378w" sizes="(max-width: 752px) 100vw, 752px" /></a>then start your VNC viewer and use below url to control your raspberry PI.

<pre class="lang:sh decode:true">raspberry_ip:0</pre>

Enjoy.

 [1]: http://www.tightvnc.com/download.php