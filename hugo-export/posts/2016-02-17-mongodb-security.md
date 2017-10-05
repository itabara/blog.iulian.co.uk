---
title: MongoDB – Security
author: Iulian
type: post
date: 2016-02-17T10:39:21+00:00
url: /2016/02/mongodb-security/
categories:
  - NoSQL
tags:
  - mongodb

---
<p style="text-align: justify;">
  By default security in mongoDB is turned off. You can enable it by using <strong>mongod &#8211;auth</strong> or add the key into mongodb.config
</p>

<pre class="lang:js decode:true">bind_ip = 0.0.0.0
port = 27017
quiet = true
dbpath=D:\mongodb\data\db
logpath=D:\mongodb\log\mongo.log
logappend = true
diaglog=3
journal = true

# Turn on/off security.  Off is currently the default
#noauth = true
<strong>auth = true
</strong></pre>

If you try any commands from mongodb, the result will look like below:

<pre class="lang:js decode:true">&gt; show collections
2016-02-17T11:38:30.409+0200 E QUERY    [thread1] Error: listCollections failed: {
        "ok" : 0,
        "errmsg" : "not authorized on admin to execute command { listCollections: 1.0, filter: {} }",
        "code" : 13
} :
_getErrorWithCode@src/mongo/shell/utils.js:23:13
DB.prototype._getCollectionInfosCommand@src/mongo/shell/db.js:746:1
DB.prototype.getCollectionInfos@src/mongo/shell/db.js:758:15
DB.prototype.getCollectionNames@src/mongo/shell/db.js:769:12
shellHelper.show@src/mongo/shell/utils.js:694:9
shellHelper@src/mongo/shell/utils.js:593:15
@(shellhelp2):1:1</pre>

However you have access to **admin** database.

<pre class="lang:js decode:true">use admin
var adminUser = {user:"admin", pwd:"admin", roles: ["userAdminAnyDatabase", "readWriteAnyDatabase"]};
db.createUser(adminUser)
db.auth("admin", "admin")</pre>

or you can try to start the shell from command line:

<pre class="lang:sh decode:true">mongo localhost/admin -u admin -p</pre>

Type of users (clients):

  * &#8220;admin&#8221; users: 
      * can do administration
      * created in the &#8220;admin&#8221; database
      * can access all databases
  * regular users: 
      * access a specific database
      * read/write or readOnly

To alter the roles:

<pre class="lang:c# decode:true">db.grantRolesToUser("admin",[{role:"readWriteAnyDatabase", db:"admin"}])
db.getUser("admin");</pre>

Available roles:

  * read
  * readWrite
  * dbAdmin
  * userAdmin
  * clusterAdmin
  * readAnyDatabase
  * readWriteAnyDatabase
  * dbAdminAnyDatabase
  * userAdminAnyDatabase

<p style="text-align: justify;">
  To secure cluster mongodb you can enable Mongodb authentication and authorization with <strong>&#8211;keyFile</strong> flag. When using &#8211;keyFile with a replica set, database contents are sent over the network between mongod nodes unencrypted.
</p>

<pre class="lang:sh decode:true">touch keyfile
chmod 600 keyfile
openssl rand -base64 60 &gt;&gt; keyfile</pre>

The keyfile must be present on all members of replicaset.

<pre class="lang:sh decode:true ">mkdir data
mkdir data2
mongod --dbpath data --auth --replSet myreplica --keyFile keyfile
mongod --port 27002 --dbpath data2 --auth --replSet myreplica --keyFile keyfile</pre>

Login and test the replica set authentication:

<pre class="lang:js decode:true">mongo localhost:27002/test -u admin -p
db
db.slaveOk()
rs.status() -- it's calling admin db so used must be authorized for admin database
db.isMaster()</pre>

Starting MongoDB 2.6 &#8211;auth is implied by &#8211;keyFile.

Additional resources:

  1. <a href="https://docs.mongodb.org/manual/security/" target="_blank">https://docs.mongodb.org/manual/security/</a>
  2. <a href="https://docs.mongodb.org/manual/tutorial/configure-ssl/" target="_blank">https://docs.mongodb.org/manual/tutorial/configure-ssl/</a>
  3. <a href="https://docs.mongodb.org/manual/reference/built-in-roles/" target="_blank">https://docs.mongodb.org/manual/reference/built-in-roles/</a>