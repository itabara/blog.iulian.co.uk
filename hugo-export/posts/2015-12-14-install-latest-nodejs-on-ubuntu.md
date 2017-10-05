---
title: Install latest nodejs on ubuntu
author: Iulian
type: post
date: 2015-12-14T12:30:28+00:00
url: /2015/12/install-latest-nodejs-on-ubuntu/
categories:
  - Linux
  - Node.JS

---
Note for myself: If you want to &#8220;dockerize&#8221; latest version of nodejs don&#8217;t forget below:

<pre class="lang:batch decode:true">sudo apt-get install curl
curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
sudo apt-get install -y nodejs</pre>

or

<pre class="lang:sh decode:true">apt-get remove --purge nodejs npm
curl -sL https://deb.nodesource.com/setup_5.x | sudo -E bash -
apt-get install nodejs</pre>

for v5.0