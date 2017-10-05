---
title: How to set up your github repository in Ubuntu
author: Iulian
type: post
date: 2013-07-12T18:39:05+00:00
url: /2013/07/how-to-set-up-your-github-repository-in-ubuntu/
categories:
  - Setup environment
tags:
  - git
  - github

---
## Setting up on Ubuntu

<p style="text-align: justify;">
  Creates a new ssh key using the github email address:
</p>

<pre class="lang:sh decode:true ">ssh-keygen -t rsa -C "itabara@live.com"</pre>

Then copy it to the clipboard:

<pre class="lang:sh decode:true">sudo apt-get install xclip
xclip -sel clip &lt; ~/.ssh/id_rsa.pub
</pre>

<p style="text-align: justify;">
  Then go to <a href="https://github.com/account/ssh">https://github.com/account/ssh</a> and click &#8220;Add another public key&#8221; and paste the clipboard and press &#8220;Add&#8221; Key&#8221;.
</p>

<p style="text-align: justify;">
  And now try to connect to github to see if everything is working:
</p>

<pre class="lang:sh decode:true">ssh-add ~/.ssh/id_rsa
ssh git@github.com</pre>

<p style="text-align: justify;">
  If everything goes fine you should have something similar:
</p>

<pre class="lang:sh decode:true">The authenticity of host 'github.com (192.30.252.128)' can't be established.
RSA key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.252.128' (RSA) to the list of known hosts.
PTY allocation request failed on channel 0
Hi itabara! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.</pre>

<p style="text-align: justify;">
  Then you should install git (it should be already installed by default):
</p>

<pre class="lang:sh decode:true ">sudo aptitude install git-core git-gui git-doc</pre>

<p style="text-align: justify;">
  Now you can configure git:
</p>

<pre class="lang:sh decode:true">git config --global user.name "Iulian Tabara"
git config --global user.email "itabara@live.com"
git config --global github.user itabara</pre>

<p style="text-align: justify;">
  Now get your token at <strong>GitHub</strong> -> <strong>Click “Settings”</strong> > <strong>Click “Personal Access Token”</strong>
</p>

<pre class="lang:sh decode:true">git config --global github.token xxxxxxxxxxxxx
</pre>

You can create a github repository and clone locally.

<pre class="lang:sh decode:true ">git clone git@github.com:itabara/hello-world.git</pre>

&nbsp;