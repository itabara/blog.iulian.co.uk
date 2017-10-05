---
title: Docker private registry
author: Iulian
type: post
date: 2016-02-14T14:24:05+00:00
url: /2016/02/docker-private-registry/
categories:
  - DevOps
tags:
  - docker; docker-compose

---
<p style="text-align: justify;">
  Docker even has a public registry called <a href="https://hub.docker.com/">Docker Hub</a> to store Docker images. While Docker lets you upload your Docker creations to their Docker Hub for free, anything you upload is also public. This might not be the best option when you work on your projects or inside your organization.
</p>

<p style="text-align: justify;">
  So I wanted to spin up my own private docker registry based on &#8230; docker. Actually <strong>docker-compose</strong> as I need to components for my repo: the docker server itself and the nginx server acting as a proxy to handle authentication and ssl.
</p>

<p style="text-align: justify;">
  Below the <strong>docker-compose.yml</strong> file I&#8217;m using to spin up the environment:
</p>

<pre class="lang:sh decode:true">nginx:
  image: "nginx:1.9"
  ports:
    - 5443:443
  links:
    - registry:registry
  volumes:
    - ./nginx/:/etc/nginx/conf.d:ro

registry:
  image: registry:2
  ports:
    - 127.0.0.1:5000:5000
  environment:
    REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY: /data
  volumes:
    - ./data:/data</pre>

On my host I created two directories to handle data images and nginx configuration.

<pre class="lang:sh decode:true ">iulian@iulitab:/usr/share/docker-registry$ ls -la
total 20
drwxr-xr-x   4 root root 4096 Mar 20 14:49 .
drwxr-xr-x 125 root root 4096 Mar  4 21:20 ..
drwxr-xr-x   3 iulian iulian 4096 Mar 20 15:16 data
-rw-r--r--   1 iulian iulian  296 Mar 20 14:32 docker-compose.yml
drwxr-xr-x   2 iulian iulian 4096 Mar 20 14:47 nginx</pre>

In nginx directory I added the configuration file nginx.conf together with the ssl certificate.

<pre class="lang:sh decode:true ">upstream docker-registry {
  server registry:5000;
}

server {
  listen 443;
  server_name registry.iulitab.co.uk;

  # SSL
  ssl on;
  ssl_certificate /etc/nginx/conf.d/iulitab.co.uk.crt;
  ssl_certificate_key /etc/nginx/conf.d/iulitab.co.uk.key;

  # disable any limits to avoid HTTP 413 for large image uploads
  client_max_body_size 0;

  # required to avoid HTTP 411: see Issue #1486 (https://github.com/docker/docker/issues/1486)
  chunked_transfer_encoding on;

  location /v2/ {
    # Do not allow connections from docker 1.5 and earlier
    # docker pre-1.6.0 did not properly set the user agent on ping, catch "Go *" user agents
    if ($http_user_agent ~ "^(docker\/1\.(3|4|5(?!\.[0-9]-dev))|Go ).*$" ) {
      return 404;
    }

    # To add basic authentication to v2 use auth_basic setting plus add_header
    auth_basic "registry.localhost";
    auth_basic_user_file /etc/nginx/conf.d/registry.password;
    add_header 'Docker-Distribution-Api-Version' 'registry/2.0' always;

    proxy_pass                          http://docker-registry;
    proxy_set_header  Host              $http_host;   # required for docker client's sake
    proxy_set_header  X-Real-IP         $remote_addr; # pass on real client's IP
    proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header  X-Forwarded-Proto $scheme;
    proxy_read_timeout                  900;
  }
}</pre>

The registry.password file is used by nginx to keep users and passwords for our registry.

Initially created with

<pre class="lang:sh decode:true">sudo htpasswd -c registry.password &lt;USER&gt;</pre>

you can strip -c and add additional users.

You can spin up the docker registry with:

<pre class="lang:sh decode:true ">docker-compose up -d</pre>

If everything is ok, you can check if platform is up:

<pre class="lang:sh decode:true">iulian@iulitab:/usr/share/docker-registry/nginx$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                           NAMES
773c4a3937c4        nginx:1.9           "nginx -g 'daemon off"   26 minutes ago      Up 26 minutes       80/tcp, 0.0.0.0:5043-&gt;443/tcp   dockerregistry_nginx_1
6703b1ac2f79        registry:2          "/bin/registry /etc/d"   26 minutes ago      Up 26 minutes       127.0.0.1:5000-&gt;5000/tcp        dockerregistry_registry_1</pre>

If you want to start docker-registry as a service you need to create the Upstart script:

<pre class="lang:sh decode:true">sudo vim /etc/init/docker-registry.conf
</pre>

<pre class="lang:sh decode:true ">description "Docker Registry"

start on runlevel [2345]
stop on runlevel [016]

respawn
respawn limit 10 5

chdir /docker-registry

exec /usr/local/bin/docker-compose up</pre>

Let&#8217;s test our new Upstart script by running:

<pre class="lang:sh decode:true">sudo service docker-registry start</pre>

Feel free to check the log files:

<pre class="lang:sh decode:true">sudo tail -f /var/log/upstart/docker-registry.log</pre>

Your private repository is accessible via <a href="https://registry.iulitab.co.uk:5443/v2/" target="_blank">https://registry.iulitab.co.uk:5443/v2/</a>

## Using our private docker registry:

You can login to your registry with:

<pre class="lang:sh decode:true">docker login https://registry.iulitab.co.uk:5443</pre>

From your **client machine**, create a small empty image to push to our new registry. We will create a simple image based on the `ubuntu` image from Docker Hub

<pre class="lang:sh decode:true">docker run -t -i ubuntu /bin/bash</pre>

Inside the container create a dummy directory (eg. my_application) and commit the change:

<pre class="code-pre command"><code langs="">docker commit $(docker ps -lq) my-ubuntu</code></pre>

then tag the image:

<pre class="lang:sh decode:true">docker tag my-ubuntu registry.iulitab.co.uk:5443/my-ubuntu</pre>

Note that you are using the local name of the image first, then the tag you want to add to it. The tag does **not** use `https://`, just the domain, port, and image name.

Now we can push that image to our registry. This time we&#8217;re using the tag name only:

<pre class="lang:sh decode:true ">docker push registry.iulitab.co.uk:5443/my-ubuntu</pre>

Similar you can pull images from your private registry:

<pre class="lang:sh decode:true">docker pull registry.iulitab.co.uk:5443/my-ubuntu</pre>

or

<pre class="lang:sh decode:true">docker run -t -i registry.iulitab.co.uk:5443/my-ubuntu /bin/bash</pre>

Happy Dockering!