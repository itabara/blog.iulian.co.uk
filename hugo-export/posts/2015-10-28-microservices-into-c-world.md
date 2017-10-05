---
title: 'Microservices into C# World ?'
author: Iulian
type: post
date: 2015-10-28T19:41:28+00:00
url: /2015/10/microservices-into-c-world/
categories:
  - Architecture

---
<p style="text-align: justify;">
  A bit of background about microservices <a href="http://martinfowler.com/articles/microservices.html" target="_blank">via Martin Fowler</a>. Some really cool stuff regarding patterns via <a href="http://microservices.io/index.html" target="_blank">Microservices.io.</a>
</p>

**Microphone **allows you to run self hosting services using either WebApi or NancyFx ontop of a Consul.io cluster.
  
You can find the code here <https://github.com/rogeralsing/Microphone>

[Consul][1] is a system that does service discovery, configuration management, and health checking for your services. Services can register themselves using either a REST API or via DNS, and if a service becomes unresponsive, Consul can mark this service as unavailable and not include it in any search results.

You can start consul service with below line on console:

<pre class="lang:ps decode:true">consul agent -server -bootstrap -data-dir /tmp/consul</pre>

Check service health on Consul agent:

<pre class="lang:ps decode:true ">http://localhost:8500/v1/agent/checks</pre>

Check all services registered on Consul agent:

<pre class="lang:ps decode:true">http://localhost:8500/v1/agent/services</pre>

Discovery service output:

<pre class="lang:js decode:true">{
   "consul": {
      "ID": "consul",
      "Service": "consul",
      "Tags": [],
      "Address": "",
      "Port": 8300
   },
   "customersc1ed8ecc-f6c6-4f74-9a19-81d22c227f78": {
      "ID": "customersc1ed8ecc-f6c6-4f74-9a19-81d22c227f78",
      "Service": "customers",
      "Tags": [
         "urlprefix-/customers"
      ],
      "Address": "BlueOne",
      "Port": 9779
   }
}</pre>

Time to play with some <a href="https://github.com/itabara/consul.io" target="_blank">code</a>.

Reference: <a href="http://blog.nethouse.se/2015/10/19/introducing-microphone-microservices-with-service-discovery-for-net/" target="_blank">Microservices discovery for .net</a>

 [1]: https://www.consul.io