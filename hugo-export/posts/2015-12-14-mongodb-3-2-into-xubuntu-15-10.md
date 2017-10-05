---
title: MongoDB 3.2 into xUbuntu 15.10
author: Iulian
type: post
date: 2015-12-14T10:05:39+00:00
url: /2015/12/mongodb-3-2-into-xubuntu-15-10/
categories:
  - Linux
tags:
  - mongodb

---
<p style="text-align: justify;">
  After I looked over the the steps from: <a href="https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/" target="_blank">https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/</a>  I wanted to start the service with:
</p>

<pre class="lang:batch decode:true">$ sudo service mongod start
</pre>

<p style="text-align: justify;">
  Unfortunately I had below error:
</p>

<p style="text-align: justify;">
  <em>Failed to start mongod.service: Unit mongod.service failed to load: No such file or directory.</em>
</p>

<p style="text-align: justify;">
  This error occurred due to the problem with the new Ubuntu (15 and ahead).
</p>

<p style="text-align: justify;">
  Default init system is <strong>systemd</strong> which was <strong>Upstart</strong> previously. So you need to install Upstart, reboot your system and here you go, you can now run mongodb service.
</p>

  * Install Upstart

<pre class="lang:batch decode:true ">sudo apt-get install upstart-sysv</pre>

  * Reboot your system

sudo service mongod start mongod start/running, process 3371

As alternative to install mongodb:

<pre class="lang:batch decode:true">echo "deb http://repo.mongodb.org/apt/debian wheezy/mongodb-org/3.0 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.0.list
sudo apt-get update
sudo apt-get install -y mongodb-org</pre>

**Edit: 05.01.2016: Just follow below steps as an alternative to fix mongod.service:**

Create a **mongod.service** into **/lib/systemd/system/**

<pre class="lang:sh decode:true ">[Unit]
Description=High-performance, schema-free document-oriented database
Documentation=man:mongod(1)
After=network.target

[Service]
Type=forking
User=mongodb
Group=mongodb
RuntimeDirectory=mongod
PIDFile=/var/run/mongod/mongod.pid
ExecStart=/usr/bin/mongod -f /etc/mongod.conf --pidfilepath /var/run/mongod/mongod.pid --fork
TimeoutStopSec=5
KillMode=mixed

[Install]
WantedBy=multi-user.target</pre>

Then run the commands:

<pre class="lang:sh decode:true">$ systemctl enable mongod.service
$ systemctl start mongod.service</pre>

My **/etc/mongod.conf** file looks like:

<pre class="lang:sh decode:true "># mongodb.conf

# Where to store the data.

# Note: if you run mongodb as a non-root user (recommended) you may
# need to create and set permissions for this directory manually,
# e.g., if the parent directory isn't mutable by the mongodb user.
dbpath=/var/lib/mongodb

#where to log
logpath=/var/log/mongodb/mongodb.log

logappend=true

#port = 27017

# Disables write-ahead journaling
# nojournal = true

# Enables periodic logging of CPU utilization and I/O wait
#cpu = true

# Turn on/off security.  Off is currently the default
#noauth = true
#auth = true

# Verbose logging output.
#verbose = true

# Inspect all client data for validity on receipt (useful for
# developing drivers)
#objcheck = true

# Enable db quota management
#quota = true

# Set oplogging level where n is
#   0=off (default)
#   1=W
#   2=R
#   3=both
#   7=W+some reads
#diaglog = 0

# Ignore query hints
#nohints = true

# Disable the HTTP interface (Defaults to localhost:28017).
#nohttpinterface = true

# Turns off server-side scripting.  This will result in greatly limited
# functionality
#noscripting = true

# Turns off table scans.  Any query that would do a table scan fails.
#notablescan = true

# Disable data file preallocation.
#noprealloc = true

# Specify .ns file size for new databases.
# nssize = &lt;size&gt;

# Accout token for Mongo monitoring server.
#mms-token = &lt;token&gt;

# Server name for Mongo monitoring server.
#mms-name = &lt;server-name&gt;

# Ping interval for Mongo monitoring server.
#mms-interval = &lt;seconds&gt;

# Replication Options

# in master/slave replicated mongo databases, specify here whether
# this is a slave or master
#slave = true
#source = master.example.com
# Slave only: specify a single database to replicate
#only = master.example.com
# or
#master = true
#source = slave.example.com

# in replica set configuration, specify the name of the replica set
# replSet = setname</pre>

&nbsp;

&nbsp;