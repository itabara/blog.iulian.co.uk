---
title: Creating home dir for already created users in Linux
author: Iulian
type: post
date: 2013-06-22T14:32:26+00:00
url: /2013/06/creating-home-dir-for-already-created-users-in-linux/
categories:
  - DevOps

---
I created some users with:

<pre class="lang:sh decode:true ">useradd iulian</pre>

<p style="text-align: justify;">
  but I forgot to specify the parameter <code>-m</code> to create the home directory and to have the skeleton files copied into it.
</p>

<p style="text-align: justify;">
  Some say <strong><code>mkhomedir_helper</code></strong> can do the trick but running it and nothing was changes.
</p>

<p style="text-align: justify;">
  I end up doing:
</p>

<pre class="lang:sh decode:true ">mkdir /home/iulian
cd /home/iulian
cp -r /etc/skel/. .
chown -R iulian.users .
chmod -R go=u,go-w .
chmod go= .
</pre>

&nbsp;

&nbsp;