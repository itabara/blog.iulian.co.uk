---
title: Docker data volumes and sharing data
author: Iulian
type: post
date: 2015-11-21T09:22:29+00:00
url: /2015/11/docker-data-volumes-and-sharing-data/
categories:
  - DevOps
tags:
  - Docker
  - Docker Volumes

---
Docker containers are temporary.

<p style="text-align: justify;">
  You can experiment this by starting a basic ubuntu image and create a test file or dir. The soon you exit the container all your changes will disappear.
</p>

<pre class="lang:sh decode:true">docker run -t -i ubuntu /bin/bash 
touch /my_file
ls -&gt; you will see my_file
exit
</pre>

Let&#8217;s try to bring that container up again:

<pre class="lang:sh decode:true">docker run -t -i ubuntu /bin/bash 
ls -&gt; my_file is GONE</pre>

<p style="text-align: justify;">
  Thankfully Docker provides the solution to keep data persistent.
</p>

<h2 style="text-align: justify;">
  Docker Data Volumes
</h2>

<p style="text-align: justify;">
  Docker data volumes are used when:
</p>

<li style="text-align: justify;">
  You want to persist data, even through container restarts
</li>
<li style="text-align: justify;">
  You want to share data between the host filesystem and the Docker container
</li>
<li style="text-align: justify;">
  You want to share data with other Docker containers.
</li>

## Data persist

<p style="text-align: justify;">
  There&#8217;s no way to directly create a &#8220;data volume&#8221; in Docker, so instead we create a <strong>data volume container</strong><em> </em>with a volume attached to it. For any other containers that you then want to connect to this data volume container, use the Docker&#8217;s &#8211;volumes-from<code></code> option to grab the volume from this container and apply them to the current container.
</p>

<p style="text-align: justify;">
  Le&#8217;s create a data volume container to store our volume:
</p>

<pre class="lang:sh decode:true">docker create -v /data --name datacontainer ubuntu
docker volume ls</pre>

<pre class="lang:sh decode:true">DRIVER              VOLUME NAME
local               c8b7031de3a7504c677b38617426ee22b28e3718c87331758d5022fd485b1e26</pre>

<p style="text-align: justify;">
  This created a container named <strong>datacontainer</strong> based on <strong>ubuntu</strong> image and in the directory <strong>/data</strong>.
</p>

<p style="text-align: justify;">
  If we reiterate our initial test with <strong>&#8211;volumes-from</strong> flag, anything we write to <strong>/data</strong> directory into current container will be saved to the <strong>/data</strong> volume of our <strong>datacontainer</strong>.
</p>

<pre class="lang:sh decode:true">docker run -t -i --volumes-from datacontainer ubuntu /bin/bash</pre>

<pre class="lang:sh decode:true">root@60c6e7f8307c:/# touch /data/my_file
exit
</pre>

Now rerun the container and check if /data/my_file is persisted.

<pre class="lang:sh decode:true ">docker run -t -i --volumes-from datacontainer ubuntu /bin/bash
ls /data/</pre>

<p style="text-align: justify;">
  You can also create as many data volume containers as you&#8217;d like but you are restricted to choose the mount inside the container (/data in our example).
</p>

<h2 style="text-align: justify;">
  Sharing data between containers &#8211; shared volumes
</h2>

<p style="text-align: justify;">
  There is the need to share data between host and container itself. Docker gives you the option to run a container and override one of its directories with the contents of a directory on the host system.
</p>

<p style="text-align: justify;">
  Let&#8217;s imagine you&#8217;re running your application and you want to keep the logs out of container.
</p>

<pre class="lang:c# decode:true">mkdir ~/nginxlogs
docker run -d -v ~/nginxlogs:/var/log/nginx -p 8080:80 -i nginx</pre>

We set up a volume that links the **/var/log/nginx** directory from the nginx container to **~/nginxlogs** on our host.

<pre class="lang:c# decode:true">curl localhost:80800
tail ~/nginxlogs/access.log</pre>

<p style="text-align: justify;">
  If you make any changes to the <strong>~/nginxlogs </strong>folder, you&#8217;ll be able to see them from inside the Docker container in real-time as well.
</p>

<p style="text-align: justify;">
  Additional links:
</p>

<li style="text-align: justify;">
  <a href="https://docs.docker.com/engine/userguide/containers/dockervolumes/" target="_blank">https://docs.docker.com/engine/userguide/containers/dockervolumes/</a>
</li>