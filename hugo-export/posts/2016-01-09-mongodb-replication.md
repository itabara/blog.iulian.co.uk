---
title: MongoDB – Replication
author: Iulian
type: post
date: 2016-01-09T14:23:15+00:00
url: /2016/01/mongodb-replication/
categories:
  - NoSQL
tags:
  - mongodb
  - replication

---
<p style="text-align: justify;">
  Replication is the process of synchronizing data across multiple servers.
</p>

<p style="text-align: justify;">
  Replica sets and scaling are used to achieve reliable and high performance deployments. Replica sets ensure multiple copies of data are available. They are build using multiple types of MongoDB nodes. Replica sets exists on<strong> odd numbers</strong> in order to allow election of a primary node. The Write operations go to primary node, reads can be distributed to the order nodes.
</p>

<p style="text-align: justify;">
  Replication benefits:
</p>

<li style="text-align: justify;">
  <strong>High Availability</strong> &#8211; automatic failover
</li>
<li style="text-align: justify;">
  <strong>Data Safety</strong> &#8211; durability, extra copies
</li>
<li style="text-align: justify;">
  <strong>Disaster recovery</strong>
</li>
<li style="text-align: justify;">
  <strong>Scaling</strong> (some situations)
</li>

Node types:

<li style="text-align: justify;">
  <strong>Primary node &#8211; </strong>writes always go to it
</li>
<li style="text-align: justify;">
  <strong>Regular node</strong> &#8211; function as secondary nodes and it can take over the role of primary node in the event of a failure.
</li>
<li style="text-align: justify;">
  <strong>Arbiter node</strong> &#8211; it doesn&#8217;t keep a copy of the data. It plays a role in the elections that select a primary if the current primary is unavailable.
</li>
<li style="text-align: justify;">
  <strong>Special purpose nodes</strong> &#8211; active backup
</li>

<p style="text-align: justify;">
  On regular nodes we can apply restrictions to keep the role of that node (only read so node will never be promoted as primary node).
</p>

<h6 style="text-align: justify;">
  <strong>Building a replica set</strong> &#8211; it requires installing MongoDB on additional hosts (I recommend an automatic tool to do the provisioning &#8211; eg: vagrant + ansible) or to use a cloud based solution. Please be aware it&#8217;s highly recommended to keep your data folder out of any container so mapping host folder to guest can be a good practice.
</h6>

<a href="https://www.iuliantabara.com/replicaset/" rel="attachment wp-att-1050"><img class="aligncenter size-full wp-image-1050" src="https://www.iuliantabara.com/wp-content/uploads/2016/02/ReplicaSet.png" alt="Replica Set" width="702" height="802" srcset="https://www.iuliantabara.com/wp-content/uploads/2016/02/ReplicaSet.png 702w, https://www.iuliantabara.com/wp-content/uploads/2016/02/ReplicaSet-263x300.png 263w" sizes="(max-width: 702px) 100vw, 702px" /></a>

<pre class="lang:js decode:true">E:\&gt;cd mongoreplica
E:\mongoreplica&gt;mkdir data-rs1
E:\mongoreplica&gt;mkdir data-rs2
E:\mongoreplica&gt;mkdir data-rs3
E:\mongoreplica&gt;mkdir data-rsarbiter
E:\mongoreplica&gt;mkdir logs

## start mongodb
e:\mongodb\bin\mongod --port 27017 --dbpath e:\mongoreplica\data-rs1\ --replSet rs0 --smallfiles --oplogSize 128 --logpath e:\mongoreplica\logs\mongo-rs1.log
e:\mongodb\bin\mongod --port 27018 --dbpath e:\mongoreplica\data-rs2\ --replSet rs0 --smallfiles --oplogSize 128 --logpath e:\mongoreplica\logs\mongo-rs2.log
e:\mongodb\bin\mongod --port 27019 --dbpath e:\mongoreplica\data-rs3\ --replSet rs0 --smallfiles --oplogSize 128 --logpath e:\mongoreplica\logs\mongo-rs3.log
e:\mongodb\bin\mongod --port 30000 --dbpath e:\mongoreplica\data-rsarbiter --replSet rs0 --smallfiles --noprealloc --nojournal --logpath e:\mongoreplica\logs\mongo-rsarbiter.log

## connect to first instance
mongo --port 27017
</pre>

Create the replica set configuration document:

<pre class="lang:js decode:true">rsconf = {
  _id: "rs0",
  members: [
    {
      _id: 0,
      host: "localhost:27017" -- use machine name / dns with appropriate TTL
    }
  ]
}

rs.initiate(rsconf)

rs.conf() -- check the configuration

// add aditional nodes
rs.add("localhost:27018")
rs.add("localhost:27019")

rs.addArb("localhost:30000")

</pre>

The configuration is showed below:

<pre class="lang:js decode:true">rs0:PRIMARY&gt; rs.status()
{
        "set" : "rs0",
        "date" : ISODate("2016-01-10T19:34:57.794Z"),
        "myState" : 1,
        "members" : [
                {
                        "_id" : 0,
                        "name" : "localhost:27017",
                        "health" : 1,
                        "state" : 1,
                        "stateStr" : "PRIMARY",
                        "uptime" : 244,
                        "optime" : Timestamp(1452454399, 1),
                        "optimeDate" : ISODate("2016-01-10T19:33:19Z"),
                        "electionTime" : Timestamp(1452454290, 2),
                        "electionDate" : ISODate("2016-01-10T19:31:30Z"),
                        "configVersion" : 4,
                        "self" : true
                },
                {
                        "_id" : 1,
                        "name" : "localhost:27018",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 139,
                        "optime" : Timestamp(1452454399, 1),
                        "optimeDate" : ISODate("2016-01-10T19:33:19Z"),
                        "lastHeartbeat" : ISODate("2016-01-10T19:34:57.645Z"),
                        "lastHeartbeatRecv" : ISODate("2016-01-10T19:34:56.841Z"),
                        "pingMs" : 0,
                        "syncingTo" : "localhost:27019",
                        "configVersion" : 4
                },
                {
                        "_id" : 2,
                        "name" : "localhost:27019",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 135,
                        "optime" : Timestamp(1452454399, 1),
                        "optimeDate" : ISODate("2016-01-10T19:33:19Z"),
                        "lastHeartbeat" : ISODate("2016-01-10T19:34:57.645Z"),
                        "lastHeartbeatRecv" : ISODate("2016-01-10T19:34:56.219Z"),
                        "pingMs" : 0,
                        "syncingTo" : "localhost:27017",
                        "configVersion" : 4
                },
                {
                        "_id" : 3,
                        "name" : "localhost:30000",
                        "health" : 1,
                        "state" : 7,
                        "stateStr" : "ARBITER",
                        "uptime" : 98,
                        "lastHeartbeat" : ISODate("2016-01-10T19:34:57.647Z"),
                        "lastHeartbeatRecv" : ISODate("2016-01-10T19:34:57.647Z"),
                        "pingMs" : 0,
                        "configVersion" : 4
                }
        ],
        "ok" : 1
}</pre>

# Verifying if failover works

<p style="text-align: justify;">
  First we&#8217;ll need to config that all nodes are running. The status of the replica set can confirm that. Then we&#8217;ll remove one server to simulate the failure and we expect the replica set to elect a primary and remain responsive.
</p>

<pre class="lang:js decode:true">tasklist | grep mongod
taskkill /pid &lt;primaryPid&gt; -- kill the primary mongoDB server</pre>

We&#8217;ll connect to the one of the running mongoDB instances (eg: port 27018).

<pre class="lang:js decode:true">rs0:PRIMARY&gt; rs.status()
{
        "set" : "rs0",
        "date" : ISODate("2016-01-10T19:45:43.733Z"),
        "myState" : 1,
        "members" : [
                {
                        "_id" : 0,
                        "name" : "localhost:27017",
                        "health" : 0,
                        "state" : 8,
                        "stateStr" : "(not reachable/healthy)",
                        "uptime" : 0,
                        "optime" : Timestamp(0, 0),
                        "optimeDate" : ISODate("1970-01-01T00:00:00Z"),
                        "lastHeartbeat" : ISODate("2016-01-10T19:45:41.158Z"),
                        "lastHeartbeatRecv" : ISODate("2016-01-10T19:42:01.751Z"),
                        "pingMs" : 0,
                        "lastHeartbeatMessage" : "Failed attempt to connect to localhost:27017; couldn't connect to server localhost:27017 (127.0.0.1), connection attempt failed",
                        "configVersion" : -1
                },
                {
                        "_id" : 1,
                        "name" : "localhost:27018",
                        "health" : 1,
                        "state" : 1,
                        "stateStr" : "PRIMARY",
                        "uptime" : 1651,
                        "optime" : Timestamp(1452454399, 1),
                        "optimeDate" : ISODate("2016-01-10T19:33:19Z"),
                        "electionTime" : Timestamp(1452454926, 1),
                        "electionDate" : ISODate("2016-01-10T19:42:06Z"),
                        "configVersion" : 4,
                        "self" : true
                },
                {
                        "_id" : 2,
                        "name" : "localhost:27019",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 780,
                        "optime" : Timestamp(1452454399, 1),
                        "optimeDate" : ISODate("2016-01-10T19:33:19Z"),
                        "lastHeartbeat" : ISODate("2016-01-10T19:45:42.988Z"),
                        "lastHeartbeatRecv" : ISODate("2016-01-10T19:45:42.380Z"),
                        "pingMs" : 0,
                        "configVersion" : 4
                },
                {
                        "_id" : 3,
                        "name" : "localhost:30000",
                        "health" : 1,
                        "state" : 7,
                        "stateStr" : "ARBITER",
                        "uptime" : 742,
                        "lastHeartbeat" : ISODate("2016-01-10T19:45:42.990Z"),
                        "lastHeartbeatRecv" : ISODate("2016-01-10T19:45:41.808Z"),
                        "pingMs" : 0,
                        "configVersion" : 4
                }
        ],
        "ok" : 1
}</pre>

<p style="text-align: justify;">
  The previous elected primary node shows as &#8220;(not reachable/healthy)&#8221;.
</p>

<p style="text-align: justify;">
  You can see the second replica node was elected as primary in few seconds.
</p>

<h2 style="text-align: justify;">
  Read concern
</h2>

<p style="text-align: justify;">
  After you finish the configuration of the replica set and everything is up and running you can start using it. If you&#8217;re performing an insert (on primary) via shell and you want to read the data from one of the secondary nodes, you will probably have an error:
</p>

<pre class="lang:js decode:true ">error:{"$err" : "not master and slaveOk=false", "code" : 13435}</pre>

You need to accept eventually inconsistent reads:

<pre class="lang:js decode:true">rs.slaveOk()</pre>

# Sample exercise

<pre class="lang:js decode:true">rs1:PRIMARY&gt; for (var i = 0; i &lt; 50000; i++)
{
   db.mycollection.insert( {_id: i}); sleep(1)
}

rs1:SECONDARY&gt;db.mycollection.count()</pre>

<p style="text-align: justify;">
  ReadPreference modes &#8211; it affects consistency and speed by telling mongoDB how to route the read requests among nodes in a replica set:
</p>

  * **primary** &#8211; default, for high data consistency requirements, all data are read from the primary.
  * **primaryPreferred** &#8211; use secondary if primary node is not responding.
  * **seconday** &#8211; go to second for the reads as primary needs to be 100% for write.
  * **secondaryPreferred** &#8211; secondary is the top of the list for reads, but you can go to primary if requested (you cannot reach any secondaries).
  * **nearest** &#8211; based on network latency (recommended on remote data center)

<p style="text-align: justify;">
  You can see an implementation in nodeJS for the ReadPreference by checking reference <a href="https://mongodb.github.io/node-mongodb-native/driver-articles/anintroductionto1_1and2_2.html" target="_blank">4</a>.
</p>

# Level of Write Concern

<p style="text-align: justify;">
  The level of write concern instructs MongoDB how to respond to writes by describing the level of acknowledgement requested from MongoDB for write operations in standalone, replica set or sharded clusters.
</p>

<p style="text-align: justify;">
  The Write concern is reflected in the consistency, redundancy and responsiveness of the entire mongoDB.
</p>

<p style="text-align: justify;">
  Write concern can tell MongoDB to act <em>synchronous</em> or <em>asynchronous</em> to persistence operations. If data consistency requirements are high then write concern should be synchronous (MongoDB will wait until data is replicated on all nodes).
</p>

<li style="text-align: justify;">
  <strong>Unacknowledged</strong> &#8211; async &#8211; send the write and immediately move. it doesn&#8217;t even wait the mongod process to confirm the request.
</li>
<li style="text-align: justify;">
  <strong>Acknowledged</strong> &#8211; it&#8217;s the default level &#8211; The mongo client will wait mongod process to confirm the request. It doesn&#8217;t mean mongod process has done anything with the request.
</li>
<li style="text-align: justify;">
  <strong>Journaled</strong> &#8211; instructs mongod process to respond only after the Write has been written to the journal on disk.
</li>
<li style="text-align: justify;">
  <strong>Replica acknowledgment</strong> &#8211; instructs mongod process to respond only after the Write has been written to the primary and to only 1 or more nodes from the replica set.
</li>

<pre class="lang:js decode:true ">use statsdb

// insert in replica set and timeout in max 3 seconds.
db.statistics.insert(
   {
    "request_ip": "127.0.0.1",
    "request_date": ISODate("2015-01-10T04:48:14.332Z"),
    "request_method": "GET",
    "request_uri": "iuliantabara.com/index.html",
    "action": "VIEW",
    "request_time_milliseconds": 379,
    cookies: ['session','admin']
   },
   { writeConcern: { w: "majority", wtimeout: 3000 }}
)
</pre>

Values for w:

  * 0 &#8211; Requests no acknowledgment of the write operation.
  * 1 &#8211; Requests acknowledgement
  * n > 1 &#8211; copies of data spread on n nodes.

For large volumes of data, sharding is the preferred method to scale as replica set is increasing the traffic.

<p style="text-align: justify;">
  Single replica set has limitation of 12 nodes (edit: raised to 50 nodes as of MongoDB 3.0)
</p>

<p style="text-align: justify;">
  You can retrieve the errors from specific each host by using
</p>

<pre class="lang:js decode:true">db.runCommand({getLastError:1, w:1, wtimeout:3000}) -- see above the values for w</pre>

<p style="text-align: justify;">
  Oplog &#8211; The <a class="reference internal" href="https://docs.mongodb.org/manual/reference/glossary/#term-oplog"><em class="xref std std-term">oplog</em></a> (operations log) is a special <a class="reference internal" href="https://docs.mongodb.org/manual/reference/glossary/#term-capped-collection"><em class="xref std std-term">capped collection</em></a> that keeps a rolling record of all operations that modify the data stored in your databases. MongoDB applies database operations on the <a class="reference internal" href="https://docs.mongodb.org/manual/reference/glossary/#term-primary"><em class="xref std std-term">primary</em></a> and then records the operations on the primary’s oplog. The <a class="reference internal" href="https://docs.mongodb.org/manual/reference/glossary/#term-secondary"><em class="xref std std-term">secondary</em></a> members then copy and apply these operations in an asynchronous process. All replica set members contain a copy of the oplog, in the <a class="reference internal" title="local.oplog.rs" href="https://docs.mongodb.org/manual/reference/local-database/#local.oplog.rs"><tt class="xref mongodb mongodb-data docutils literal"><span class="pre">local.oplog.rs</span></tt></a> collection, which allows them to maintain the current state of the database.
</p>

<pre class="lang:js decode:true">use oplog
show collections
db.oplog.rs.find() -- show the content of oplog collection
</pre>

References:

<a href="http://docs.mongodb.org/manual/replication/" target="_blank">1.http://docs.mongodb.org/manual/replication/</a>

<a href="https://docs.mongodb.org/manual/tutorial/deploy-replica-set-for-testing/" target="_blank">2.https://docs.mongodb.org/manual/tutorial/deploy-replica-set-for-testing/</a>

3.<a href="https://docs.mongodb.org/v3.0/reference/write-concern/" target="_blank">https://docs.mongodb.org/v3.0/reference/write-concern/</a>

4.<a href="https://mongodb.github.io/node-mongodb-native/driver-articles/anintroductionto1_1and2_2.html" target="_blank">https://mongodb.github.io/node-mongodb-native/driver-articles/anintroductionto1_1and2_2.html</a>

&nbsp;