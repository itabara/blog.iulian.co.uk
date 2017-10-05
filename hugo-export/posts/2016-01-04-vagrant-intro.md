---
title: Vagrant – Intro
author: Iulian
type: post
date: 2016-01-04T12:54:56+00:00
url: /2016/01/vagrant-intro/
categories:
  - DevOps
  - Setup environment
tags:
  - Vagrant
  - VirtualBox

---
<p style="text-align: justify;">
  Vagrant makes it possible to quickly create a virtual environment for development. It is different than cloning or snapshots in that it uses minimal base OSes and provides a provisioning mechanism to setup and configure the environment exactly the way you want for development.
</p>

<p style="text-align: justify;">
  Benefits:
</p>

<li style="text-align: justify;">
  All developers work in the exact same environment
</li>
  * Developers can get a new environment up in minutes
  * Developers don’t need to be experts at setting up the environment.
  * System details can be versioned and stored alongside code.

You need to create a single [Vagrantfile][1] and everything will be configured as you requested.

<p style="text-align: justify;">
  Vagrant is using different provides but by default <a href="http://www.virtualbox.org/">VirtualBox</a> is the free and easy to use.
</p>

<pre class="lang:ruby decode:true">$ vagrant init hashicorp/precise32
$ vagrant up
$ vagrant ssh
$ vagrant destroy</pre>

We&#8217;ll use a separate work folder to keep things together.

<pre class="lang:ruby decode:true">$ mkdir vagrant
$ cd vagrant
$ vagrant init</pre>

You can download any vagrant image and keep it locally for future instantiation.

<pre class="lang:ruby decode:true ">vagrant box add hashicorp/precise32

vagrant box list</pre>

A huge list of vagrant images are available on [HashiCorp&#8217;s Atlas box catalog][2].

<pre class="lang:ruby decode:true">Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/precise32"
  config.vm.network :forwarded_port, guest: 80, host: 8000
  config.vm.provision "fix-no-tty", type: "shell" do |s|
    s.privileged = false
    s.inline = "sudo sed -i '/tty/!s/mesg n/tty -s \\&\\& mesg n/' /root/.profile"
  end
  config.vm.provision :shell, path:"bootstrap.sh"
end</pre>

The above script includes the fix for

    stdin: is not a tty
    

**bootstrap.ssh**

<pre class="lang:sh decode:true">#!/usr/bin/env bash

apt-get update
apt-get install -y apache2
if ! [ -L /var/www ]; then
  rm -rf /var/www
  ln -fs /vagrant /var/www
fi</pre>

If vagrant image is running you need to use

<pre class="lang:ruby decode:true ">vagrant reload --provision
vagrant ssh</pre>

You can see the vagrant instance by browsing http://localhost:8000/

To share (you need to register to <https://atlas.hashicorp.com/vagrant>)

<pre class="lang:ps decode:true ">vagrant login
vagrant share</pre>

You can see it via http://delighted-jackal-2074.vagrantshare.com

To stop the VM:

<pre class="lang:ps decode:true ">vagrant halt</pre>

or

<pre class="lang:ps decode:true ">vagrant suspend -- complete shutdown</pre>

 [1]: http://docs.vagrantup.com/v2/vagrantfile/
 [2]: https://atlas.hashicorp.com/boxes/search