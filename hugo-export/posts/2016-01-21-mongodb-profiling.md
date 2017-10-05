---
title: MongoDB – Profiling and performance
author: Iulian
type: post
date: 2016-01-21T03:06:30+00:00
url: /2016/01/mongodb-profiling/
categories:
  - DevOps
  - NoSQL
tags:
  - mongodb

---
You can retrieve the status of your server by using:

<pre class="lang:js decode:true">db.serverStatus()</pre>

See the list of running operations in current shell:

<pre class="lang:js decode:true">db.currentOp() -- "secs_running": (look for long times)
db.killOp(opid)
</pre>

<pre class="lang:js decode:true">db.currentOp().inprog.forEach(
    function(op){
        if (op.secs_running &gt; 10){
             print("Slow op in progress? secs:" + op.secs_running + " opid:" + op.opid);
        }
    }
)</pre>

Cautions:

  * Exercise care killing operations on secondaries (replica set)
  * Exercise care killing compact operations
  * Don&#8217;t kill internal operations &#8211; data migration / sync

## [mongostat][1]

<p style="text-align: justify;">
  The <tt class="xref mongodb mongodb-program docutils literal"><span class="pre">mongostat</span></tt> utility provides a quick overview of the status of a currently running <a class="reference internal" title="mongod" href="https://docs.mongodb.org/manual/reference/program/mongod/#bin.mongod"><tt class="xref mongodb mongodb-program docutils literal"><span class="pre">mongod</span></tt></a> or <a class="reference internal" title="mongos" href="https://docs.mongodb.org/manual/reference/program/mongos/#bin.mongos"><tt class="xref mongodb mongodb-program docutils literal"><span class="pre">mongos</span></tt></a> instance.
</p>

<pre class="lang:js decode:true ">insert query update delete getmore command % dirty % used flushes  vsize    res qr|qw ar|aw netIn netOut conn                      time
    *0    *0     *0     *0       0     1|0     0.0    2.4       0 484.0M 209.0M   0|0   0|0   79b    18k    2 2016-01-21T16:45:01+02:00
    *0    *0     *0     *0       0     1|0     0.0    2.4       0 484.0M 209.0M   0|0   0|0   79b    18k    2 2016-01-21T16:45:02+02:00
    *0    *0     *0     *0       0     1|0     0.0    2.4       0 484.0M 209.0M   0|0   0|0   79b    18k    2 2016-01-21T16:45:03+02:00
    *0    *0     *0     *0       0     1|0     0.0    2.4       0 484.0M 209.0M   0|0   0|0   79b    18k    2 2016-01-21T16:45:04+02:00
    *0    *0     *0     *0       0     2|0     0.0    2.4       0 484.0M 209.0M   0|0   0|0  133b    18k    2 2016-01-21T16:45:05+02:00
    *0    *0     *0     *0       0     1|0     0.0    2.4       0 484.0M 209.0M   0|0   0|0   79b    18k    2 2016-01-21T16:45:06+02:00
    *0    *0     *0     *0       0     1|0     0.0    2.4       0 484.0M 209.0M   0|0   0|0   79b    18k    2 2016-01-21T16:45:07+02:00
    *0    *0     *0     *0       0     1|0     0.0    2.4       0 484.0M 209.0M   0|0   0|0   79b    18k    2 2016-01-21T16:45:08+02:00
    *0    *0     *0     *0       0     1|0     0.0    2.4       0 484.0M 209.0M   0|0   0|0   79b    18k    2 2016-01-21T16:45:09+02:00
    *0    *0     *0     *0       0     2|0     0.0    2.4       0 484.0M 209.0M   0|0   0|0  133b    18k    2 2016-01-21T16:45:10+02:00</pre>

## <a href="https://docs.mongodb.org/manual/reference/program/mongotop/" target="_blank">mongotop</a>

<p style="text-align: justify;">
  <tt class="xref mongodb mongodb-program docutils literal"><span class="pre"><tt>M</tt>ongotop</span></tt> tracks the amount of time a MongoDB instance spends reading and writing data.
</p>

<pre class="lang:js decode:true ">ns    total    read    write    2016-01-21T16:44:28+02:00
       admin.system.roles      0ms     0ms      0ms
     admin.system.version      0ms     0ms      0ms
      example.isItCovered      0ms     0ms      0ms
           example.mycoll      0ms     0ms      0ms
 example.system.namespace      0ms     0ms      0ms
example.system.namespaces      0ms     0ms      0ms
   example.system.profile      0ms     0ms      0ms
               loc.places      0ms     0ms      0ms
    local.listCollections      0ms     0ms      0ms
        local.startup_log      0ms     0ms      0ms</pre>

&nbsp;

## Profiler

<p style="text-align: justify;">
  The database profiler collects fine grained data about MongoDB operations, cursors, and commands. Profiling can be enabled at database level or per MongoDB instance.
</p>

<p style="text-align: justify;">
  Levels of profiling :
</p>

<li style="text-align: justify;">
  0 = off, no profiling
</li>
<li style="text-align: justify;">
  2 = on
</li>
<li style="text-align: justify;">
  1 = selective, show only slow operations ( > slowms)
</li>

<pre class="lang:js decode:true">show profile
db.showProfilingLevel(1, 10) -- slower than 10ms
db.showProfilingLevel(2)
db.getProfilingStatus() -- display current profile setting

show collections</pre>

A &#8220;new&#8221; collection will be visible. As a note, the system.profile collection is <a href="https://docs.mongodb.org/manual/core/capped-collections/" target="_blank">capped</a>.

<pre class="lang:js decode:true">&gt; show collections
mycoll
system.profile

&gt; db.system.profile.find().pretty()
</pre>

The output:

<pre class="lang:js decode:true">{
        "op" : "query",
        "ns" : "example.system.profile",
        "query" : {
                "find" : "system.profile",
                "filter" : {

                }
        },
        "keysExamined" : 0,
        "docsExamined" : 4,
        "cursorExhausted" : true,
        "keyUpdates" : 0,
        "writeConflicts" : 0,
        "numYield" : 0,
        "locks" : {
                "Global" : {
                        "acquireCount" : {
                                "r" : NumberLong(2)
                        }
                },
                "Database" : {
                        "acquireCount" : {
                                "r" : NumberLong(1)
                        }
                },
                "Collection" : {
                        "acquireCount" : {
                                "r" : NumberLong(1)
                        }
                }
        },
        "nreturned" : 4,
        "responseLength" : 3113,
        "protocol" : "op_command",
        "millis" : 0,
        "execStats" : {
                "stage" : "COLLSCAN",
                "filter" : {
                        "$and" : [ ]
                },
                "nReturned" : 4,
                "executionTimeMillisEstimate" : 0,
                "works" : 6,
                "advanced" : 4,
                "needTime" : 1,
                "needYield" : 0,
                "saveState" : 0,
                "restoreState" : 0,
                "isEOF" : 1,
                "invalidates" : 0,
                "direction" : "forward",
                "docsExamined" : 4
        },
        "ts" : ISODate("2016-01-21T14:18:27.768Z"),
        "client" : "127.0.0.1",
        "allUsers" : [ ],
        "user" : ""
}</pre>

<pre class="lang:js decode:true">&gt; db.getCollectionInfos()
[
        {
                "name" : "mycoll",
                "options" : {

                }
        },
        {
                "name" : "system.profile",
                "options" : {
                        "capped" : true,
                        "size" : 1048576
                }
        }
]</pre>

Retrieve the last profile info:

<pre class="lang:js decode:true">db.system.profile.find().sort({$natural:-1}).limit(1).pretty()
db.system.profile.find({},{op:1}).sort({$natural:-1}).limit(10).pretty() -- show last 10 operations
</pre>

Additional links:

  1. <a href="https://docs.mongodb.org/manual/reference/command/profile/" target="_blank">https://docs.mongodb.org/manual/reference/command/profile/</a>

 [1]: https://docs.mongodb.org/manual/reference/program/mongostat/