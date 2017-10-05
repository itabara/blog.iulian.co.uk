---
title: 'Private: Microservices with nodejs and docker'
author: Iulian
type: post
date: 2015-12-09T13:06:32+00:00
draft: true
private: true
url: /2015/12/microservices-with-nodejs-and-docker/
categories:
  - Architecture
  - DevOps

---
Let&#8217;s start a fresh new instance of ubuntu container under docker and install nodejs.

<pre class="lang:batch decode:true">docker run -i -t ubuntu ./bin/bash

vi installnode.sh

apt-get update
apt-get install --yes nodejs
apt-get install --yes nodejs-legacy
apt-get install --yes npm

bash installnode.sh


</pre>

<pre class="lang:batch decode:true ">docker run -it -v //c/Users/demo/projects/my_microservice/:/host -p 9000:3000 ubuntu-node:0.1 ./bin/bash</pre>

<pre class="lang:batch decode:true">root@1fb30f8c3eda:/# cp -r /host /microservice</pre>

<pre class="lang:batch decode:true ">BLUEONE+demo@BlueOne MINGW64 ~
$ docker commit -a iulian 1fb3 node-microservice:0.1
b6ff74bac8f863c694fc9e03d2b91d4bc63bb4bef2f52085e3e22a02a043d1cd

BLUEONE+demo@BlueOne MINGW64 ~
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
node-microservice   0.1                 b6ff74bac8f8        7 seconds ago       399.9 MB
ubuntu-node         0.1                 a526b046051e        21 minutes ago      392.9 MB
ubuntu              latest              89d5d8e8bafb        18 hours ago        187.9 MB
hello-world         latest              0a6ba66e537a        8 weeks ago         960 B
</pre>

Start the image in background:

<pre class="lang:batch decode:true ">BLUEONE+demo@BlueOne MINGW64 ~
$ docker commit -a iulian 1fb3 node-microservice:0.1
b6ff74bac8f863c694fc9e03d2b91d4bc63bb4bef2f52085e3e22a02a043d1cd

BLUEONE+demo@BlueOne MINGW64 ~
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
node-microservice   0.1                 b6ff74bac8f8        7 seconds ago       399.9 MB
ubuntu-node         0.1                 a526b046051e        21 minutes ago      392.9 MB
ubuntu              latest              89d5d8e8bafb        18 hours ago        187.9 MB
hello-world         latest              0a6ba66e537a        8 weeks ago         960 B
</pre>

<pre class="lang:batch decode:true">BLUEONE+demo@BlueOne MINGW64 /d/blog/nodejs/my_microservice (master)
$ docker attach 5718
GET /api/sayhello 304 1.700 ms - -</pre>

<pre class="lang:batch decode:true">BLUEONE+demo@BlueOne MINGW64 ~
$ docker run -d -w //microservice -p 9000:3000 node-microservice:0.1 npm start
138c33d4572a850ee96efbb48caece0ce9547209774dd911f3964f658d884155</pre>

Inspect the running container image:

<pre class="lang:batch decode:true ">docker ps
docker inspect &lt;containerid&gt;</pre>

&nbsp;

&nbsp;