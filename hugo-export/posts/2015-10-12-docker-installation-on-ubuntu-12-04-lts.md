---
title: Docker installation on Ubuntu 12.04 LTS
author: Iulian
type: post
date: 2015-10-12T03:14:53+00:00
url: /2015/10/docker-installation-on-ubuntu-12-04-lts/
categories:
  - DevOps
tags:
  - Docker

---
<p style="text-align: justify;">
  Docker requires a 64-bit installation regardless of your Ubuntu version. Additionally, your kernel must be 3.10 at minimum.
</p>

<p style="text-align: justify;">
  To check current kernel version:
</p>

<pre class="lang:sh decode:true ">root@cs29634620:/# uname -r
3.13.0-76-generic
</pre>

<pre class="lang:sh decode:true">sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D</pre>

Create `/etc/apt/sources.list.d/docker.list and add below line:`

<pre class="lang:sh decode:true ">deb https://apt.dockerproject.org/repo ubuntu-precise main</pre>

<pre class="lang:sh decode:true">apt-get update
apt-get purge lxc-docker
apt-cache policy docker-engine</pre>

<p style="text-align: justify;">
  For Ubuntu Trusty, Vivid, and Wily, it’s recommended to install the <code>linux-image-extra</code> kernel package. The <code>linux-image-extra</code> package allows you use the <code>aufs</code> storage driver.
</p>

<pre class="lang:sh decode:true">apt-get install linux-image-generic-lts-trusty
reboot</pre>

Install docker:

<pre class="lang:sh decode:true ">apt-get install docker-engine
service docker start

docker run hello-world</pre>

Later-edit:

To avoid : Cannot connect to the Docker daemon. Is the docker daemon running on this host?

<pre class="lang:sh decode:true ">sudo usermod -aG docker &lt;your-username&gt;</pre>

<p style="text-align: justify;">
  <strong>Log out and log in back. This ensures your user is running with the correct permissions.</strong>
</p>

Enjoy.

Additional links:

  1. <a href="https://docs.docker.com/engine/installation/ubuntulinux/" target="_blank">https://docs.docker.com/engine/installation/ubuntulinux/</a>

<p style="text-align: justify;">