---
title: Docker – Intro
author: Iulian
type: post
date: 2015-10-09T12:49:14+00:00
url: /2015/10/docker-intro/
categories:
  - DevOps
  - Linux
tags:
  - Docker

---
<h2 style="text-align: justify;">
  Intro
</h2>

<p style="text-align: justify;">
  <a href="https://www.docker.com/" target="_blank">Docker</a> provides an integrated technology suite that enables development and IT operations teams to build, ship, and run distributed applications anywhere.
</p>

<p style="text-align: justify;">
  &#8220;Docker wasn&#8217;t on anyone&#8217;s roadmap in 2014, it is on everyone&#8217;s roadmap for 2015&#8221; <a class="twitter-atreply pretty-link js-nav" dir="ltr" href="https://twitter.com/adrianco" data-mentioned-user-id="13895242"><s>@</s><b>adrianco</b></a>
</p>

## Terminology

<li style="text-align: justify;">
  <strong>Docker Images</strong> &#8211; blueprints of our application
</li>
<li style="text-align: justify;">
  <strong>Docker Container</strong> &#8211; created from docker images and are real instances of our application
</li>
<li style="text-align: justify;">
  <strong>Docker Daemon</strong> &#8211; building, running and distributing Docker images
</li>
<li style="text-align: justify;">
  <strong>Docker Client</strong> &#8211; Run on our local machine and connect to the daemon
</li>
<li style="text-align: justify;">
  <strong>Docker Hub</strong> &#8211; a registry of docker images
</li>

## Installation

Quick and easy install script provided by Docker:

<pre class="lang:sh decode:true ">curl -sSL https://get.docker.com/ | sh</pre>

<p style="text-align: justify;">
  If you&#8217;re looking for detailed steps or how to install on different Windows / OSX please check <a href="https://docs.docker.com/" target="_blank">https://docs.docker.com/</a>
</p>

<p style="text-align: justify;">
  If you&#8217;re running docker under Linux you need to run
</p>

<pre class="lang:batch decode:true">sudo usermod -aG docker &lt;your-username&gt;</pre>

<p style="text-align: justify;">
  to avoid: Cannot connect to the Docker daemon. Is the docker daemon running on this host? <strong>Log out and log in back. This ensures your user is running with the correct permissions.</strong>
</p>

<p style="text-align: justify;">
  On Linux the 3.10.x kernel is <a href="https://docs.docker.com/installation/binaries/#check-kernel-dependencies">the minimum requirement</a> for Docker. For OSX 10.8 “Mountain Lion” or newer is required.
</p>

<p style="text-align: justify;">
  Both OSX and Windows require Docker Toolbox to be installed in order to be able to run docker containers.
</p>

<p style="text-align: justify;">
  Early versions of Docker Toolbox require you to install a VM with Docker Machine using the VirtualBox provider:
</p>

<pre class="lang:sh decode:true">$ docker-machine create --driver=virtualbox default
$ docker-machine ls
$ eval "$(docker-machine env default)"</pre>

However the latest versions of Docker Toolbox will take care of this part. You can check **Docker-machine** version and status and ip:

<pre class="lang:batch decode:true">$ docker-machine version
C:\Program Files\Docker Toolbox\docker-machine.exe version 0.5.2 ( 0456b9f )

$ docker-machine env default
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="C:\Users\demo\.docker\machine\machines\default"
export DOCKER_MACHINE_NAME="default"
# Run this command to configure your shell:
# eval "$(C:\Program Files\Docker Toolbox\docker-machine.exe env default)"</pre>

<pre class="lang:batch decode:true ">BLUEONE+demo@BlueOne MINGW64 ~
$ docker-machine status default
Running</pre>

## **Docker Images**

<p style="text-align: justify;">
  Docker at its core is a way to separate an application and the dependencies needed to run it from the operating system itself. To make this possible Docker uses <em>containers</em> and <em>images</em>.
</p>

<p style="text-align: justify;">
  A Docker image is basically a <a href="https://docs.docker.com/engine/understanding-docker/#how-does-a-docker-image-work">template </a>for a filesystem. When you run a Docker image, an instance of this filesystem is made live and runs on your system inside a Docker container. By default this container can&#8217;t touch the original image itself or the filesystem of the host where Docker is running. It&#8217;s a self-contained environment.
</p>

Let&#8217;s search the official Ubuntu docker image.

  * <a href="https://docs.docker.com/reference/commandline/imageshttps://docs.docker.com/engine/reference/commandline/search/" target="_blank">docker search</a> &#8211; search images by name``

<pre class="lang:batch decode:true">$ docker search ubuntu
NAME                           DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
ubuntu                         Ubuntu is a Debian-based Linux operating s...   2789      [OK]
ubuntu-upstart                 Upstart is an event-based replacement for ...   48        [OK]
sequenceiq/hadoop-ubuntu       An easy way to try Hadoop on Ubuntu             26                   [OK]
torusware/speedus-ubuntu       Always updated official Ubuntu docker imag...   25                   [OK]
ubuntu-debootstrap             debootstrap --variant=minbase --components...   20        [OK]
tleyden5iwx/ubuntu-cuda        Ubuntu 14.04 with CUDA drivers pre-installed    18                   [OK]
rastasheep/ubuntu-sshd         Dockerized SSH service, built on top of of...   15                   [OK]
n3ziniuka5/ubuntu-oracle-jdk   Ubuntu with Oracle JDK. Check tags for ver...   5                    [OK]
sameersbn/ubuntu                                                               5                    [OK]
nuagebec/ubuntu                Simple always updated Ubuntu docker images...   4                    [OK]
nimmis/ubuntu                  This is a docker images different LTS vers...   3                    [OK]
maxexcloo/ubuntu               Docker base image built on Ubuntu with Sup...   2                    [OK]
seetheprogress/ubuntu          Ubuntu image provided by seetheprogress us...   1                    [OK]
sylvainlasnier/ubuntu          Ubuntu 15.04 root docker images with commo...   1                    [OK]
isuper/base-ubuntu             This is just a small and clean base Ubuntu...   1                    [OK]
densuke/ubuntu-jp-remix        Ubuntu Linuxremix                       1                    [OK]
rallias/ubuntu                 Ubuntu with the needful                         0                    [OK]
tvaughan/ubuntu                https://github.com/tvaughan/docker-ubuntu       0                    [OK]
esycat/ubuntu                  Ubuntu LTS                                      0                    [OK]
konstruktoid/ubuntu            Ubuntu base image                               0                    [OK]
zoni/ubuntu                                                                    0                    [OK]
teamrock/ubuntu                TeamRock's Ubuntu image configured with AW...   0                    [OK]
birkof/ubuntu                  Ubuntu 14.04 LTS (Trusty Tahr)                  0                    [OK]
partlab/ubuntu                 Simple Ubuntu docker images.                    0                    [OK]
densuke/ubuntu-supervisor      densuke/ubuntu-jp-remix:trusty  supe...       0                    [OK]
</pre>

<p style="text-align: justify;">
  Limit the search to Ubuntu images with at least 1000 stars/rating:
</p>

<pre class="lang:batch decode:true">BLUEONE+demo@BlueOne MINGW64 ~
$ docker search -s 1000 ubuntu
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
ubuntu    Ubuntu is a Debian-based Linux operating s...   2789      [OK]</pre>

  * <a href="https://www.iuliantabara.com/2016/02/mongodb-security/" target="_blank">docker run</a> &#8211; run and interact with the image:

<pre class="lang:batch decode:true">BLUEONE+demo@BlueOne MINGW64 ~
$ docker run -it ubuntu ./bin/bash
root@1fd0654363a9:/#</pre>

  * <a href="https://docs.docker.com/engine/reference/commandline/exec/" target="_blank">docker exec</a> &#8211; SSH on a container:

<pre class="lang:batch decode:true">docker exec -i -t &lt;instance_id&gt; bash</pre>

  * [docker images ][1]&#8211; shows the list of docker images:

<pre class="lang:batch decode:true">BLUEONE+demo@BlueOne MINGW64 ~
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
ubuntu              latest              89d5d8e8bafb        18 hours ago        187.9 MB
hello-world         latest              0a6ba66e537a        8 weeks ago         960 B</pre>

  * <a href="https://docs.docker.com/engine/reference/commandline/ps/" target="_blank">docker ps</a> &#8211; List of running instances:

<pre class="lang:batch decode:true">BLUEONE+demo@BlueOne MINGW64 ~
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
1fd0654363a9        ubuntu              "./bin/bash"        10 minutes ago      Up 10 minutes                           serene_panini</pre>

View previous images and history:

<pre class="lang:batch decode:true">BLUEONE+demo@BlueOne MINGW64 ~
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                        PORTS                    NAMES
1fd0654363a9        ubuntu              "./bin/bash"        12 minutes ago      Exited (127) 12 seconds ago                            serene_panini
ec638626f2c4        ubuntu              "-t"                2 hours ago         Created                                                suspicious_poincare
59262b38036c        ubuntu              "/bin/bash"         2 hours ago         Exited (0) 2 hours ago                                 sad_swirles
cbeafb0682f9        8574d741d86b        "/bin/bash"         5 days ago          Exited (1) 5 days ago                                  tender_newton</pre>

<div class="page-header">
  <ul>
    <li>
      <a href="https://docs.docker.com/engine/reference/commandline/inspect/" target="_blank">docker inspect</a> &#8211; how to retrieve Docker container&#8217;s internal IP address:
    </li>
  </ul>
</div>

<pre class="lang:batch decode:true">docker inspect &lt;container_id&gt;
</pre>

or

<pre class="lang:batch decode:true">docker inspect -f '{{ .NetworkSettings.IPAddress }}' &lt;container_id&gt;</pre>

  * [`docker import`][2] creates an image from a tarball.
  * [`docker build`][3] creates image from Dockerfile.
  * [`docker commit`][4] creates image from a container, pausing it temporarily if it is running.

<pre class="lang:batch decode:true">BLUEONE+demo@BlueOne MINGW64 ~
$ docker commit -a itabara 1fd0654363a9 ubuntu-node:0.1
a526b046051e64ac8cc943cec1afe2210cbee99c7202ea728a77c027a55b0bc1</pre>

  * [`docker rmi`][5] removes an image.

<pre class="lang:batch decode:true">docker rmi -f "image name/ image id"</pre>

Remove docker instance:

<pre class="lang:batch decode:true"># docker ps -a

# docker rm  daf644660736</pre>

&nbsp;

<p style="text-align: justify;">
  While you can use the <code>docker rmi</code> command to remove specific images, there&#8217;s a tool called <a href="https://github.com/spotify/docker-gc">docker-gc</a> that will clean up images that are no longer used by any containers in a safe manner.
</p>

  * [`docker load`][6] loads an image from a tar archive as STDIN, including images and tags (as of 0.7).
  * [`docker save`][7] saves an image to a tar archive stream to STDOUT with all parent layers, tags & versions (as of 0.7).
  * [`docker history`][8] shows history of image.
  * [`docker tag`][9] tags an image to a name (local or registry).

&nbsp;

## [][10]{#user-content-containers.anchor}Containers

<p style="text-align: justify;">
  Docker containers wrap up your application in a complete filesystem that contains everything it needs to run: code, runtime, system tools, system libraries – anything you can install on a server. This guarantees that it will always run the same, regardless of the environment it is running in.
</p>

## Containers are for software, not for data

<p style="text-align: justify;">
  Docker containers are consumables. Docker containers should be used to scale applications, for firing more app servers with the same setup etc. Data belongs onto the filesystem but not into a container that can neither be cloned in an easy way nor incrementally backed up in a reasonable way. Containers are for software, not for data.
</p>

## Docker container commands

  * [`docker create`][11] creates a container but does not start it.
  * [`docker run`][12] creates and starts a container in one operation.
  * [`docker rm`][13] deletes a container.
  * [`docker update`][14] updates a container&#8217;s resource limits.

<p style="text-align: justify;">
  If you want to map a directory on the host to a docker container, <code>docker run -v $HOSTDIR:$DOCKERDIR</code>. Also see <a href="https://github.com/wsargent/docker-cheat-sheet/#volumes">Volumes</a>.
</p>

  * [`docker start`][15] starts a container so it is running.
  * [`docker stop`][16] stops a running container.
  * [`docker restart`][17] stops and starts a container.
  * [`docker pause`][18] pauses a running container, &#8220;freezing&#8221; it in place.
  * [`docker unpause`][19] will unpause a running container.
  * [`docker wait`][20] blocks until running container stops.
  * [`docker kill`][21] sends a SIGKILL to a running container.
  * [`docker attach`][22] will connect to a running container.
  * ``[`docker logs`][23] gets logs from container. (You can use a custom log driver, but logs is only available for `json-file` and `journald` in 1.10)
  * [`docker inspect`][24] looks at all the info on a container (including IP address).
  * [`docker events`][25] gets events from container.
  * [`docker port`][26] shows public facing port of container.
  * [`docker top`][27] shows running processes in container.
  * [`docker stats`][28] shows containers&#8217; resource usage statistics.
  * [`docker diff`][29] shows changed files in the container&#8217;s FS.
  * `docker stats --all` shows a running list of containers.

<p style="text-align: justify;">
  Additional links:
</p>

<li style="text-align: justify;">
  <a href="https://docs.docker.com/linux/" target="_blank">https://docs.docker.com/linux/</a>
</li>
<li style="text-align: justify;">
  <a href="https://docs.docker.com/engine/understanding-docker/#how-does-a-docker-image-work" target="_blank">https://docs.docker.com/engine/understanding-docker/#how-does-a-docker-image-work</a>
</li>
<li style="text-align: justify;">
  <a href="https://docs.docker.com/engine/userguide/containers/usingdocker/" target="_blank">https://docs.docker.com/engine/userguide/containers/usingdocker/</a>
</li>

 [1]: https://docs.docker.com/reference/commandline/images
 [2]: https://docs.docker.com/reference/commandline/import
 [3]: https://docs.docker.com/reference/commandline/build
 [4]: https://docs.docker.com/reference/commandline/commit
 [5]: https://docs.docker.com/reference/commandline/rmi
 [6]: https://docs.docker.com/reference/commandline/load
 [7]: https://docs.docker.com/reference/commandline/save
 [8]: https://docs.docker.com/reference/commandline/history
 [9]: https://docs.docker.com/reference/commandline/tag
 [10]: https://github.com/wsargent/docker-cheat-sheet#containers
 [11]: https://docs.docker.com/reference/commandline/create
 [12]: https://docs.docker.com/reference/commandline/run
 [13]: https://docs.docker.com/reference/commandline/rm
 [14]: https://docs.docker.com/engine/reference/commandline/update/
 [15]: https://docs.docker.com/reference/commandline/start
 [16]: https://docs.docker.com/reference/commandline/stop
 [17]: https://docs.docker.com/reference/commandline/restart
 [18]: https://docs.docker.com/engine/reference/commandline/pause/
 [19]: https://docs.docker.com/engine/reference/commandline/unpause/
 [20]: https://docs.docker.com/reference/commandline/wait
 [21]: https://docs.docker.com/reference/commandline/kill
 [22]: https://docs.docker.com/reference/commandline/attach
 [23]: https://docs.docker.com/reference/commandline/logs
 [24]: https://docs.docker.com/reference/commandline/inspect
 [25]: https://docs.docker.com/reference/commandline/events
 [26]: https://docs.docker.com/reference/commandline/port
 [27]: https://docs.docker.com/reference/commandline/top
 [28]: https://docs.docker.com/reference/commandline/stats
 [29]: https://docs.docker.com/reference/commandline/diff