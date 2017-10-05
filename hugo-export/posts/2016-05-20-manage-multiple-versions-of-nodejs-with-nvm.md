---
title: Manage multiple versions of nodejs with nvm
author: Iulian
type: post
date: 2016-05-20T20:33:33+00:00
url: /2016/05/manage-multiple-versions-of-nodejs-with-nvm/
categories:
  - Nodejs
tags:
  - nvm

---
<p style="text-align: justify;">
  I&#8217;m using nodejs as my primary development language and usually I deploy my projects in docker containers. There days I realised my development nodejs version is not the latest one.
</p>

<pre class="lang:sh decode:true">$ node -v
v5.5.0</pre>

<p style="text-align: justify;">
  So I was looking to update it really easy with the option to choose between the current version (reminds me on .net framework version via app.pool or php versioning).
</p>

[**Nvm**][1] seems the perfect tool to do the job:

<pre class="lang:sh decode:true">$ git clone git://github.com/creationix/nvm.git ~/nvm
$ . ~/nvm/nvm.sh
$ nvm install 6.1</pre>

<pre class="lang:sh decode:true">Downloading https://nodejs.org/dist/v6.1.0/node-v6.1.0-darwin-x64.tar.gz...
######################################################################## 100.0%
Now using node v6.1.0 (npm v3.8.6)
Creating default alias: default -&gt; 6.1 (-&gt; v6.1.0)</pre>

<pre class="lang:sh decode:true ">$ nvm use 6.1</pre>

Now using node v6.1.0 (npm v3.8.6)

<pre class="lang:sh decode:true">$ nvm alias default 6.1</pre>

default -> 6.1 (-> v6.1.0)

<pre class="lang:sh decode:true">$ node -v</pre>

The output was **v6.1.0**

You can check the installed node.js versions with:

<pre class="lang:sh decode:true ">$ nvm ls</pre>

&nbsp;

 [1]: https://github.com/creationix/nvm