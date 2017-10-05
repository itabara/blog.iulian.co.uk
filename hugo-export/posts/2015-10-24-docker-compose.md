---
title: Docker compose
author: Iulian
type: post
date: 2015-10-24T08:58:13+00:00
url: /2015/10/docker-compose/
categories:
  - DevOps
tags:
  - Docker
  - Docker-compose

---
<p style="text-align: justify;">
  Docker is a great tool but for complex applications with a lot of components, orchestrating all the containers to start up and shut down together (not to mention talk to each other) can quickly become difficult.
</p>

<p style="text-align: justify;">
  Docker Compose makes dealing with the orchestration processes of Docker containers (such as starting up, shutting down, and setting up intracontainer linking and volumes) really easy.
</p>

## Installing Docker Compose {#step-2-—-installing-docker-compose}

<pre class="lang:sh decode:true  ">apt-get -y install python-pip
apt-get install libpq-dev python-dev
pip install docker-compose

/usr/bin/python2 --version
/usr/bin/python --version

apt-get install python python-pip python-requests
pip install functools32
pip install -U websocket

docker-compose --version</pre>

## Running a Container with Docker Compose {#step-3-—-running-a-container-with-docker-compose}

<pre class="lang:sh decode:true">mkdir hello-world
cd hello-world
vim docker-compose.yml</pre>

**docker-compose.yml**

<pre class="lang:sh decode:true ">my-test:
  image: hello-world</pre>

While still in the<span class="Apple-converted-space"> </span>`~/hello-world`<span class="Apple-converted-space"> </span>directory, execute the following command to create the container:

<pre class="lang:sh decode:true">docker-compose up
docker-compose up -d -- to fork in background</pre>

<p style="text-align: justify;">
  To show the group of Docker containers (both stopped and currently running), use the following command:
</p>

<pre class="lang:sh decode:true ">docker-compose ps</pre>

<p style="text-align: justify;">
  To stop all running Docker containers for an application group, run the following command in the same directory as the<span class="Apple-converted-space"> </span><code>docker-compose.yml</code><span class="Apple-converted-space"> </span>file used to start the Docker group:
</p>

<pre class="lang:sh decode:true">docker-compose stop - stop the instances
docker-compose kill -- force shutdown</pre>

If you want to start from scratch you can use the<span class="Apple-converted-space"> </span>`rm`<span class="Apple-converted-space"> </span>command to fully delete all the containers that make up your container group:

<pre class="lang:sh decode:true">docker-compose rm</pre>

Additional links:

  1. <a href="https://docs.docker.com/compose/" target="_blank">https://docs.docker.com/compose/</a>

&nbsp;

&nbsp;