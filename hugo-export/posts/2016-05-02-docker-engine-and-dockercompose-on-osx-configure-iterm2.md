---
title: Docker Engine and DockerCompose on OSX – configure iterm2
author: Iulian
type: post
date: 2016-05-01T22:35:58+00:00
url: /2016/05/docker-engine-and-dockercompose-on-osx-configure-iterm2/
categories:
  - DevOps
tags:
  - Docker

---
After you install docker toolbox via

<pre class="lang:sh decode:true">brew cask install dockertoolbox</pre>

just add below on your iterm console and add support for zsh

<pre class="lang:sh decode:true">echo '\neval "$(docker-machine env default)"' &gt;&gt; ~/.zshrc && source ~/.zshrc</pre>

&nbsp;