---
title: MongoDB – Import, Backup and Database Restoration
author: Iulian
type: post
date: 2016-01-03T18:18:55+00:00
url: /2016/01/mongodb-import-backup-restore/
categories:
  - NoSQL
tags:
  - mongodb

---
**Import bulk** data into mongoDB:

<pre class="lang:js decode:true">mongoimport --db zipcodes --collection zips --file e:\blog\mongodb\zips.js</pre>

The import file looks like:

<pre class="lang:js decode:true">{ "_id" : "01001", "city" : "AGAWAM", "loc" : [ -72.622739, 42.070206 ], "pop" : 15338, "state" : "MA" }
{ "_id" : "01002", "city" : "CUSHMAN", "loc" : [ -72.51564999999999, 42.377017 ], "pop" : 36963, "state" : "MA" }
{ "_id" : "01005", "city" : "BARRE", "loc" : [ -72.10835400000001, 42.409698 ], "pop" : 4546, "state" : "MA" }</pre>

**Bulk operations** (useful in sharded environment): Ordered and Unordered

<pre class="lang:js decode:true">var bulk = db.items.initializeUnorderedBulkOp();
bulk.insert({item:"abc123", defaultQty:100, status:"A", quantity:100}); -- not hitting the server yet
bulk.insert({item:"def456", defaultQty:200, status:"B", quantity:200});
bulk.insert({item:"ghi789", defaultQty:300, status:"C", quantity:300});
bulk.execute() -- now we'll hit it</pre>

The output:

<pre class="lang:js decode:true ">BulkWriteResult({
        "writeErrors" : [ ],
        "writeConcernErrors" : [ ],
        "nInserted" : 2,
        "nUpserted" : 0,
        "nMatched" : 0,
        "nModified" : 0,
        "nRemoved" : 0,
        "upserted" : [ ]
})</pre>

**Backup** the database:

<pre class="lang:js decode:true">mongodump --db zipcodes --out e:\</pre>

The backup process can be done while database is operational.

> <p style="text-align: left;">
>   ! The .bson files can be versioned via git.
> </p>

To **restore** the database:

<pre class="lang:js decode:true">show dbs

use zipcodes

db.dropDatabase()

mongorestore.exe -d zipcodes "e:/zipcodes"
</pre>

<p style="text-align: justify;">
  Restore of the documents is conditioned by _id: ObjectId(); If the document already exists into current collection, the restore document will be skipped.
</p>

<p style="text-align: justify;">
  In order to find and remove cities based on condition use below statements:
</p>

<pre class="lang:js decode:true">db.zips.find({city:"CHICOPEE"});
db.zips.remove({city:"CHICOPEE"});</pre>

###### MongoDB Backup options:

  * One mongod process with mongodump
<li style="text-align: justify;">
  One mongod process with file backup system (file system snapshot using LVM (journaling enabled !!!!!)
</li>
<li style="text-align: justify;">
  Two or more mongod process with redundant data (running replica 1 | running replica 2) &#8211; provides resilience to system failure. Multiple machines are identified as nodes in a replica set. These nodes communicate to identify a <strong>master</strong> to handle write requests and <strong>slaves</strong> for read requests. All data is replicated to all nodes.
</li>
<li style="text-align: justify;">
  One mongod process with MMS backup &#8211; point-in-time backup
</li>

<p style="text-align: justify;">
  If you want to create a hot backup you can use <strong>mongodump &#8211;oplog</strong> and restore with <strong>mongorestore &#8211;oplogReplay</strong>.
</p>

<p style="text-align: justify;">
  For sharded clusters, there are few ops you need to perform during backup:
</p>

<pre class="lang:c# decode:true"># Backup a sharded cluster

# wait for any migrations that are currently occurring to end and prevent 
# initiation of new migrations
mongo --host any_mongos_host --eval "sh.stopBalancer()"
# mare sure that worked!!!!!

# backup the config database / a config server.
mongodump -- host config_server_host --db config

# backup all the shards (for each shard, any server)
mongodump --host shard1_srv1 --oplog /backups/cluster1/shard1
mongodump --host shard2_srv1 --oplog /backups/cluster1/shard2
mongodump --host shard3_srv1 --oplog /backups/cluster1/shard3
mongodump --host shard4_srv1 --oplog /backups/cluster1/shard4

# balanced on
mongo --host any_mongos_host --eval "sh.startBalancer()"</pre>

&nbsp;