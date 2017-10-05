---
title: MongoDB – Sharding
author: Iulian
type: post
date: 2016-01-12T00:40:00+00:00
url: /2016/01/mongodb-sharding/
categories:
  - NoSQL
tags:
  - mongodb

---
<p style="text-align: justify;">
  Sharding is the process of storing data records across multiple nodes when having demands of data growth. MongoDB is solves the problem with horizontal scaling by using the sharding mechanism.
</p>

<p style="text-align: justify;">
  Compared with <a href="http://www.iuliantabara.com/2016/01/mongodb-replication/">replica set</a> there are some changes from the client perspective. The client no longer talks with <strong>mongod</strong> instances directly. Instead a new component <strong>mongos</strong> is added. <strong>Mongos</strong> is aware where the data is stored after the shard (data partition) and it&#8217;s routing the queries to the appropriate mongod proceses. Usually mongos runs alongaside with the application client or on a very light environment as it&#8217;s only job is to route the queries.
</p>

<p style="text-align: justify;">
  Mongos is using <strong>config servers</strong> (at least 3 servers to be deployed for reliability) to retrieve metadata about sharded data. Each config server is storing the same configuration.
</p>

> <p style="text-align: justify;">
>   MongoDB 3.2 deprecates the use of three mirrored <a class="reference internal" title="mongod" href="https://docs.mongodb.org/manual/reference/program/mongod/#bin.mongod"><tt class="xref mongodb mongodb-program docutils literal"><span class="pre">mongod</span></tt></a> instances for config servers.
> </p>

<p style="text-align: justify;">
  Starting in MongoDB 3.2, config servers for sharded clusters can be deployed as a <a class="reference internal" href="https://docs.mongodb.org/manual/core/replication-introduction/"><em>replica set</em></a>. The replica set config servers must run the <a class="reference internal" href="https://docs.mongodb.org/manual/core/wiredtiger/"><em>WiredTiger storage engine</em></a>.
</p>

<p style="text-align: justify;">
  A complete replica set is required for each shard to provide reliability and fail-over at the data level.
</p>

<p style="text-align: justify;">
  The config servers will ensure an even distribution of data across shards.
</p>

<p style="text-align: justify;">
  <strong>Shard key</strong> &#8211; it is a field (compound field) in a document that will be used to partition the data.
</p>

<p style="text-align: justify;">
  Shard key selection:
</p>

<li style="text-align: justify;">
  the shard key is the common in queries for the collection
</li>
<li style="text-align: justify;">
  good &#8220;cardinality&#8221; / granularity
</li>
<li style="text-align: justify;">
  consider compound key shard keys
</li>
<li style="text-align: justify;">
  is the key monotonic increasing ? (eg. _id, timestamp)
</li>

<p style="text-align: justify;">
  I&#8217;m going to use <a href="https://help.ubuntu.com/community/Screen" target="_blank">screen</a> under the xubuntu.
</p>

<pre class="lang:batch decode:true ">sudo https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.2.1.tgz

tar xzvf mongodb-linux-x86_64-3.2.1.tgz
mv mongodb-linux-x86_64-3.2.1 /mongo-shard

# build the first replica set
cd /mongo-shard
mkdir logs
mkdir -p data/s1/rs1
mkdir -p data/s1/rs2
mkdir -p data/s1/rsarbiter

# start mongod
screen -d -m -S mongo-s1-rs1 bin/mongod --port 27017 --dbpath data/s1/rs1 --replSet rs1 --smallfiles --oplogSize 128 --logpath logs/mongo-s1-rs2.log
screen -d -m -S mongo-s1-rs2 bin/mongod --port 27018 --dbpath data/s1/rs2 --replSet rs0 --smallfiles --oplogSize 128 --logpath logs/mongo-s1-rs2.log
screen -d -m -S mongo-s1-rsarbiter bin/mongod --port 30000 --dbpath data/s1/rsarbiter --replSet rs0 --smallfiles --oplogSize 128 --logpath logs/mongo-s1-rsarbiter.log

screen -ls</pre>

The output should look like below:

<pre class="lang:batch decode:true">root@iulian-pc:/mongo-shard# screen -ls
There are screens on:
	3429.mongo-s1-rsarbiter	(12.01.2016 03:31:31)	(Detached)
	3341.mongo-s1-rs2	(12.01.2016 03:29:19)	(Detached)
	3310.mongo-s1-rs1	(12.01.2016 03:28:21)	(Detached)
3 Sockets in /var/run/screen/S-root.</pre>

Let&#8217;s connect to the node rs1 and configure replica set rs0:

<pre class="lang:js decode:true">mongo --port 27017

# configure replica set rs0
rsconf = {
  _id: "rs0",
  members: [
    {
      _id: 0,
      host: "localhost:27017"
    }
  ]
}

rs.initiate(rsconf)
rs.add("localhost:27018")
rs.addArb("localhost:30000")
rs.status()</pre>

The output for rs.status() should look like below:

<pre class="lang:js decode:true">rs0:PRIMARY&gt; rs.status()
{
	"set" : "rs0",
	"date" : ISODate("2016-01-12T01:37:09.189Z"),
	"myState" : 1,
	"term" : NumberLong(1),
	"heartbeatIntervalMillis" : NumberLong(2000),
	"members" : [
		{
			"_id" : 0,
			"name" : "localhost:27017",
			"health" : 1,
			"state" : 1,
			"stateStr" : "PRIMARY",
			"uptime" : 528,
			"optime" : {
				"ts" : Timestamp(1452562617, 1),
				"t" : NumberLong(1)
			},
			"optimeDate" : ISODate("2016-01-12T01:36:57Z"),
			"infoMessage" : "could not find member to sync from",
			"electionTime" : Timestamp(1452562583, 2),
			"electionDate" : ISODate("2016-01-12T01:36:23Z"),
			"configVersion" : 3,
			"self" : true
		},
		{
			"_id" : 1,
			"name" : "localhost:27018",
			"health" : 1,
			"state" : 2,
			"stateStr" : "SECONDARY",
			"uptime" : 26,
			"optime" : {
				"ts" : Timestamp(1452562617, 1),
				"t" : NumberLong(1)
			},
			"optimeDate" : ISODate("2016-01-12T01:36:57Z"),
			"lastHeartbeat" : ISODate("2016-01-12T01:37:07.486Z"),
			"lastHeartbeatRecv" : ISODate("2016-01-12T01:37:08.486Z"),
			"pingMs" : NumberLong(0),
			"syncingTo" : "localhost:27017",
			"configVersion" : 3
		},
		{
			"_id" : 2,
			"name" : "localhost:30000",
			"health" : 1,
			"state" : 7,
			"stateStr" : "ARBITER",
			"uptime" : 11,
			"lastHeartbeat" : ISODate("2016-01-12T01:37:07.486Z"),
			"lastHeartbeatRecv" : ISODate("2016-01-12T01:37:07.634Z"),
			"pingMs" : NumberLong(0),
			"configVersion" : 3
		}
	],
	"ok" : 1
}</pre>

We&#8217;re going to import data into rs0, primary node:

<pre class="lang:js decode:true ">use logsdb
load("bulk-logs.js")
db.logs.count()</pre>

Let&#8217;s create the second replica set  rs1 by following the same steps:

<pre class="lang:batch decode:true">mkdir -p data/s2/rs1
mkdir -p data/s2/rs2
mkdir -p data/s2/rsarbiter

# start mongod
screen -d -m -S mongo-s2-rs1 bin/mongod --port 27019 --dbpath data/s2/rs1 --replSet rs1 --smallfiles --oplogSize 128 --logpath logs/mongo-s2-rs1.log

screen -d -m -S mongo-s2-rs2 bin/mongod --port 27020 --dbpath data/s2/rs2 --replSet rs1 --smallfiles --oplogSize 128 --logpath logs/mongo-s2-rs2.log

screen -d -m -S mongo-s2-rsarbiter bin/mongod --port 30001 --dbpath data/s2/rsarbiter --replSet rs1 --smallfiles --noprealloc --nojournal --logpath logs/mongo-s2-rsarbiter.log

screen -ls</pre>

The output so far:

<pre class="lang:batch decode:true">root@iulian-pc:/mongo-shard# screen -ls
There are screens on:
	4616.mongo-s2-rsarbiter	(12.01.2016 03:54:45)	(Detached)
	4592.mongo-s2-rs2	(12.01.2016 03:53:04)	(Detached)
	4567.mongo-s2-rs1	(12.01.2016 03:52:09)	(Detached)
	3429.mongo-s1-rsarbiter	(12.01.2016 03:31:32)	(Detached)
	3341.mongo-s1-rs2	(12.01.2016 03:29:20)	(Detached)
	3310.mongo-s1-rs1	(12.01.2016 03:28:22)	(Detached)
6 Sockets in /var/run/screen/S-root.
</pre>

Now let&#8217;s configure replica set rs1 buy connecting to it&#8217;s primary node:

<pre class="lang:js decode:true">mongo --port 27019

# configure replica set rs1
rsconf = {
  _id: "rs1",
  members: [
    {
      _id: 0,
      host: "localhost:27019"
    }
  ]
}

rs.initiate(rsconf)
rs.add("localhost:27020")
rs.addArb("localhost:30001")
rs.status()
</pre>

The output is similar to replica set rs0:

<pre class="lang:js decode:true ">rs1:PRIMARY&gt; rs.status()
{
	"set" : "rs1",
	"date" : ISODate("2016-01-12T02:01:34.143Z"),
	"myState" : 1,
	"term" : NumberLong(1),
	"heartbeatIntervalMillis" : NumberLong(2000),
	"members" : [
		{
			"_id" : 0,
			"name" : "localhost:27019",
			"health" : 1,
			"state" : 1,
			"stateStr" : "PRIMARY",
			"uptime" : 566,
			"optime" : {
				"ts" : Timestamp(1452564088, 1),
				"t" : NumberLong(1)
			},
			"optimeDate" : ISODate("2016-01-12T02:01:28Z"),
			"infoMessage" : "could not find member to sync from",
			"electionTime" : Timestamp(1452564071, 2),
			"electionDate" : ISODate("2016-01-12T02:01:11Z"),
			"configVersion" : 3,
			"self" : true
		},
		{
			"_id" : 1,
			"name" : "localhost:27020",
			"health" : 1,
			"state" : 2,
			"stateStr" : "SECONDARY",
			"uptime" : 17,
			"optime" : {
				"ts" : Timestamp(1452564088, 1),
				"t" : NumberLong(1)
			},
			"optimeDate" : ISODate("2016-01-12T02:01:28Z"),
			"lastHeartbeat" : ISODate("2016-01-12T02:01:32.170Z"),
			"lastHeartbeatRecv" : ISODate("2016-01-12T02:01:33.170Z"),
			"pingMs" : NumberLong(0),
			"syncingTo" : "localhost:27019",
			"configVersion" : 3
		},
		{
			"_id" : 2,
			"name" : "localhost:30001",
			"health" : 1,
			"state" : 7,
			"stateStr" : "ARBITER",
			"uptime" : 5,
			"lastHeartbeat" : ISODate("2016-01-12T02:01:32.170Z"),
			"lastHeartbeatRecv" : ISODate("2016-01-12T02:01:33.480Z"),
			"pingMs" : NumberLong(0),
			"configVersion" : 3
		}
	],
	"ok" : 1
}
rs1:PRIMARY&gt;</pre>

Now it&#8217;s the time to create the config servers:

<pre class="lang:batch decode:true"># start the config database servers
mkdir -p data/configdb1
mkdir -p data/configdb2
mkdir -p data/configdb3

screen -d -m -S mongo-cfg1 bin/mongod --configsvr --port 27119 --dbpath data/configdb1 --logpath logs/mongo-cfg1.log
screen -d -m -S mongo-cfg2 bin/mongod --configsvr --port 27120 --dbpath data/configdb2 --logpath logs/mongo-cfg2.log
screen -d -m -S mongo-cfg3 bin/mongod --configsvr --port 27121 --dbpath data/configdb3 --logpath logs/mongo-cfg3.log

screen -ls</pre>

The output should be similar to below:

<pre class="lang:batch decode:true ">root@iulian-pc:/mongo-shard# screen -ls
There are screens on:
	5185.mongo-cfg3	(12.01.2016 04:12:38)	(Detached)
	5164.mongo-cfg2	(12.01.2016 04:12:08)	(Detached)
	5143.mongo-cfg1	(12.01.2016 04:11:16)	(Detached)
	4616.mongo-s2-rsarbiter	(12.01.2016 03:54:44)	(Detached)
	4592.mongo-s2-rs2	(12.01.2016 03:53:03)	(Detached)
	4567.mongo-s2-rs1	(12.01.2016 03:52:08)	(Detached)
	3429.mongo-s1-rsarbiter	(12.01.2016 03:31:31)	(Detached)
	3341.mongo-s1-rs2	(12.01.2016 03:29:19)	(Detached)
	3310.mongo-s1-rs1	(12.01.2016 03:28:21)	(Detached)
9 Sockets in /var/run/screen/S-root.
</pre>

Not it&#8217;s time to start the **mongos** server. This will be used by all clients to query our mongoDB sharded platform.

<pre class="lang:batch decode:true">screen -d -m -S mongos bin/mongos --configdb localhost:27119,localhost:27120,localhost:27121 --port 27200

# connect to mongod and setup shard
mongo --port 27200

# from mongo shell connected to mongos, add shards
sh.addShard("rs0/localhost:27017")
sh.addShard("rs1/localhost:27019")

use logs
sh.enableSharding("logsdb")</pre>

Before sharding the collection we create the index for ShardingKey:

<pre class="lang:js decode:true">mongos&gt; db.logs.ensureIndex({request_ip:1, _id:1})
{
	"raw" : {
		"rs0/localhost:27017,localhost:27018" : {
			"createdCollectionAutomatically" : false,
			"numIndexesBefore" : 1,
			"numIndexesAfter" : 2,
			"ok" : 1,
			"$gleStats" : {
				"lastOpTime" : Timestamp(1452565470, 1),
				"electionId" : ObjectId("569458970000000000000001")
			}
		}
	},
	"ok" : 1
}</pre>

We&#8217;re ready to shard out collection by request\_ip and \_id.

<pre class="lang:js decode:true">sh.shardCollection("logsdb.logs", {"request_ip": 1, "_id": 1})

db.logs.getShardDistribution() -- to see the distribution of data
</pre>

The initial distribution of data:

<pre class="lang:batch decode:true">Shard rs0 at rs0/localhost:27017,localhost:27018
 data : 775KiB docs : 2500 chunks : 1
 estimated data per chunk : 775KiB
 estimated docs per chunk : 2500

Totals
 data : 775KiB docs : 2500 chunks : 1
 Shard rs0 contains 100% data, 100% docs in cluster, avg obj size on shard : 317B
</pre>

<p style="text-align: justify;">
  At this stage rs0 is keeping all our data. Try to generate some additional data to see the behavior of the shard.
</p>

<pre class="lang:js decode:true ">mongos&gt; db.logs.getShardDistribution()

Shard rs0 at rs0/localhost:27017,localhost:27018
 data : 13.24MiB docs : 47425 chunks : 6
 estimated data per chunk : 2.2MiB
 estimated docs per chunk : 7904

Shard rs1 at rs1/localhost:27019,localhost:27020
 data : 171KiB docs : 558 chunks : 1
 estimated data per chunk : 171KiB
 estimated docs per chunk : 558

Totals
 data : 13.41MiB docs : 47983 chunks : 7
 Shard rs0 contains 98.75% data, 98.83% docs in cluster, avg obj size on shard : 292B
 Shard rs1 contains 1.24% data, 1.16% docs in cluster, avg obj size on shard : 314B
</pre>

## Best practices on sharding:

  * only shard the big collections
  * pick sharding key carefully, they aren&#8217;t easily changeable ( it require creation of a new collection and data copy)
  * consider <a href="https://docs.mongodb.org/v3.0/tutorial/split-chunks-in-sharded-cluster/" target="_blank">pre-split</a> if bulk inserts
  * be aware of monotonically increasing shard keys (eg: timestamp, _id)
  * adding shards is easy but isn&#8217;t instantaneous
  * always connect to mongos instance and use mongod only for some dba operations when you want to talk directly with a certain server
  * keep non-mongos processes off of 27017 to avoid mistakes
  * use logical server names for config servers instead of IPs and hostnames.

References:

1.<a href="http://docs.mongodb.org/manual/sharding/" target="_blank">http://docs.mongodb.org/manual/sharding/</a>

2.<a href="https://docs.mongodb.org/manual/tutorial/deploy-shard-cluster/" target="_blank">https://docs.mongodb.org/manual/tutorial/deploy-shard-cluster/</a>