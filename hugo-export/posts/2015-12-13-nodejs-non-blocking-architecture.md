---
title: NodeJS Non-blocking architecture
author: Iulian
type: post
date: 2015-12-13T10:16:58+00:00
url: /2015/12/nodejs-non-blocking-architecture/
categories:
  - Node.JS
tags:
  - NodeJS

---
<p style="text-align: justify;">
  JavaScript is a dynamic, object-oriented, and functional scripting language. One of the features that make it win over Java Applets in the browser scripting war decades ago, it was its lightness and non-blocking event loop.
</p>

<p style="text-align: justify;">
  Bocking means that when one line of code is executing the rest of it is locked waiting to finish. On the other hand, non-blocking gives to each line of code a shot and then through callbacks it can come back when an event happens. Programming languages that are blocking (Java, Ruby, Python, PHP, …) overcomes concurrency using multiple threads of execution while JavaScript handles it using non-blocking event loop in a single thread.
</p>

<p style="text-align: justify;">
  <img class="shrinkToFit" src="http://adrianmejia.com/images/threading_java.png" alt="http://adrianmejia.com/images/threading_java.png" />
</p>

<p style="text-align: justify;">
  <img src="http://adrianmejia.com/images/threading_node.png" alt="" />
</p>

<p style="text-align: center;">
  <small>Image from <a href="http://strongloop.com/strongblog/node-js-is-faster-than-java/">strongloop.com</a></small>
</p>

<p style="text-align: justify;">
  Some companies like <a href="https://www.paypal-engineering.com/2013/11/22/node-js-at-paypal/">Paypal</a> moved from Java backend to NodeJS and reported a increased performance, lower average response times, and development speed gains. Similarly happens to <a href="https://engineering.groupon.com/2013/misc/i-tier-dismantling-the-monoliths/">Groupon</a> that came from Java/Rails monoliths.
</p>

<p style="text-align: center;">