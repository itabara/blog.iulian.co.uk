---
title: Vagrant on 123-reg Cloud server
author: Iulian
type: post
date: 2016-01-25T14:48:44+00:00
url: /2016/01/vagrant-on-123-reg-cloud-server/
categories:
  - DevOps

---
<p style="text-align: justify;">
  To install Vagrant on your <a href="https://www.123-reg.co.uk/cloud-server-hosting/" target="_blank">cloud server</a>, you need to download and run the installation kit. Before going further, be sure that you have dpkg and Virtual box installed:
</p>

<pre class="lang:sh decode:true">sudo apt-get install dpkg-dev virtualbox-dkms</pre>

<p style="text-align: justify;">
  Go to the <a href="http://downloads.vagrantup.com/" target="_blank">downloads page</a> of Vagrant and check for the latest release, download and install it:
</p>

<pre class="lang:sh decode:true ">wget https://releases.hashicorp.com/vagrant/1.8.1/vagrant_1.8.1_x86_64.deb
dpkg -i vagrant_1.8.1_x86_64.deb</pre>

<p style="text-align: justify;">
  Next you’ll need to install Kernel headers:
</p>

<pre class="lang:sh decode:true ">sudo apt-cache search linux-headers-$(uname -r)
sudo apt-get install linux-headers-$(uname -r)</pre>

<p style="text-align: justify;">
  Then, reconfigure the VirtualBox DKMS:
</p>

<pre class="lang:sh decode:true">sudo dpkg-reconfigure virtualbox-dkms</pre>

<p style="text-align: justify;">
  If everything is ok, you should be able to type into your shell:
</p>

<pre class="lang:sh decode:true ">iulian@iulian:/home/iulian$ vagrant -v
Vagrant 1.8.1
</pre>

<p style="text-align: justify;">
  Feel free to look on <a href="http://www.iuliantabara.com/tag/vagrant/" target="_blank">other articles</a> for some quick demos and capabilities of vagrant.
</p>

&nbsp;

&nbsp;