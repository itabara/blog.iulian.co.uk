---
title: iTerm3 and docker startup
author: Iulian
type: post
date: 2016-08-17T21:02:48+00:00
url: /2016/08/iterm3-and-docker-startup/
categories:
  - DevOps
  - Setup environment
tags:
  - Docker
  - iterm2

---
<p style="text-align: justify;">
  If you open iTerm2 and try to run <strong>docker ps</strong> you&#8217;ll probably receive below error:
</p>

<p style="text-align: justify;">
  Cannot connect to the Docker daemon. Is the docker daemon running on this host?
</p>

<p style="text-align: justify;">
  The solution to use iTerm2 instead of Docker QuickStart Terminal is relatively simple:
</p>

<pre class="lang:sh decode:true ">docker-machine start default</pre>

That will generate below output:

<pre class="lang:sh decode:true ">Starting "default"...
(default) Check network to re-create if needed...
(default) Waiting for an IP...
Machine "default" was started.
Waiting for SSH to be available...
Detecting the provisioner...
Started machines may have new IP addresses. You may need to re-run the `docker-machine env` command.</pre>

You then need to run:

<pre class="lang:sh decode:true ">docker-machine env</pre>

The output of it is:

<pre class="lang:sh decode:true ">export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/Users/Iulian/.docker/machine/machines/default"
export DOCKER_MACHINE_NAME="default"
# Run this command to configure your shell:
# eval $(docker-machine env)</pre>

All you need to do now is to run:

<pre class="lang:sh decode:true ">eval $(docker-machine env)
docker ps</pre>

That&#8217;s all 😉

&nbsp;