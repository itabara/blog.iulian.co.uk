---
title: 'MongoDB – Internals & Performance'
author: Iulian
type: post
date: 2016-01-19T19:58:43+00:00
url: /2016/01/mongodb-internals-performance/
categories:
  - NoSQL
tags:
  - mongodb

---
## Storage Engine

<p style="text-align: justify;">
  Starting version 3.0, MongoDB adopted a pluggable architecture allowing the option to choose the storage engine. A storage engine is the part of a database that is responsible for managing how data is stored, both in memory and on disk.
</p>

<p style="text-align: justify;">
  Different engines perform better for specific workloads, one storage engine might offer better performance for read-heavy workloads, and another might support a higher-throughput for write operations.
</p>

<p style="text-align: justify;">
  <strong><a class="reference internal" href="https://docs.mongodb.org/manual/core/mmapv1/">MMAPv1</a></strong>  &#8211; the original MongoDB storage engine and is the default storage engine for MongoDB versions before 3.2. It maps the data files directly to virtual memory allowing the operting system to do the most of the work of the storage engine.
</p>

<p style="text-align: justify;">
  <strong><a class="reference internal" href="https://docs.mongodb.org/manual/core/wiredtiger/">WiredTiger</a></strong> &#8211; default storage engine starting in MongoDB 3.2. Details can be found <a href="http://www.wiredtiger.com/" target="_blank">here</a>.
</p>

The storage engine determine:

<li style="text-align: justify;">
  <strong>data format</strong> &#8211; different storage engines can implement different types of compression, and different ways of storing the BSON for mongoDB
</li>
<li style="text-align: justify;">
  <strong>format of indexes</strong> &#8211; indexes are controlled by the storage engine. MongoDB uses <a href="https://en.wikipedia.org/wiki/B-tree" target="_blank">Btrees</a>. With MongoDB 3.0, WiredTiger is using B+ trees, with other formats expected to come in later releases.
</li>

To retrieve the current storage engine you can run below command:

<pre class="lang:js decode:true ">db.serverStatus() -- we look for storageEngine</pre>

<pre class="lang:js decode:true ">....
"storageEngine" : {
                "name" : "mmapv1",
                "supportsCommittedReads" : false
},
....</pre>

<h2 style="text-align: justify;">
  MMAPv1
</h2>

<p style="text-align: justify;">
  MMAPv1 uses <a href="https://en.wikipedia.org/wiki/Mmap" target="_blank">mmap</a> unix system call that maps files on disk directly into virtual memory space. This treats the data files like they were already in the memory.
</p>

<p style="text-align: justify;">
  MMAPv1 provides collection-level locking starting MongoDB 3.0 compared to database-level v2.2 & v2.6. MongoDB implements multiple readers &#8211; single writer locks.
</p>

<p style="text-align: justify;">
  By using journal (write ahead-log) MongoDB ensures consistency of the data. Using journal you write what you are about to do, then you do it. So if a disk failure occur while performing fsync() to the disk, the storage engine doesn&#8217;t perform the update.
</p>

<p style="text-align: justify;">
  See <a class="reference internal" href="https://docs.mongodb.org/manual/core/journaling/">Journaling</a> for more information about the journal in MongoDB.
</p>

<p style="text-align: justify;">
  By default, MongoDB uses <a class="reference internal" href="https://docs.mongodb.org/v3.0/core/mmapv1/#power-of-2-allocation">Power of 2 Sized Allocations</a> so that every document in MongoDB is stored in a record which contains the document itself and extra space, or <a class="reference internal" href="https://docs.mongodb.org/v3.0/reference/glossary/#term-padding">padding</a>. Padding allows the document to grow as the result of updates while minimizing the likelihood of reallocation.
</p>

<h2 style="text-align: justify;">
  WiredTiger
</h2>

<p style="text-align: justify;">
  WiredTiger Storage Engine is the first pluggable storage engine and brings few new features to MongoDB:
</p>

<li style="text-align: justify;">
  Document Level Locking &#8211; good concurrency protocol &#8211; you can technically achieve no locks and writes could scale with the number of threads (assuming no update to the same document or limit the threads to number of cores).
</li>
  * Compression
  * It locks some pitfalls of MMAPv1.
  * Big performance gains

To swich MongoDB to use WiredTiger simply start mongod with:

<pre class="lang:batch decode:true">mongod --storageEngine wiredTiger</pre>

<p style="text-align: justify;">
  Please be aware your existing mongoDB server should not contain any MMAPv1 existing databases into /data/db/.
</p>

<p style="text-align: justify;">
  WiredTiger stores data on disk in <a href="https://en.wikipedia.org/wiki/B-tree" target="_blank">Btrees</a>, similar with Btrees used by MMAPv1 is using for indexes. New writes are initially separate, performed on files on unused regions  and incorporated later in the background.
</p>

<p style="text-align: justify;">
  During an update, WiredTiger writes a new version of documents rather then overwriting existing data. So you don&#8217;t need to be worried about <strong>document moving</strong> or <strong>padding factor</strong>.
</p>

<p style="text-align: justify;">
  WiredTiger provides two caches:
</p>

<li style="text-align: justify;">
  WiredTiger Cache (WT Cache) &#8211;  half of your RAM (default)
</li>
<li style="text-align: justify;">
  File System Cache (FS Cache)
</li>

<p style="text-align: justify;">
  <strong>Checkpoints</strong> &#8211; act as recovery points and are handle the &#8220;transfer&#8221; data from WT cache to FS Cache and then to the disk. During a checkpoint data goes from the WT Cache to FS Cache and then flushed to disk. It initiates a new check point 60s after the end of the last checkpoint. Each checkpoint is a consistent shapshot of your data. During the write of a new checkpoint, the previous checkpoint is still valid. As such, even if MongoDB terminates or encounters an error while writing a new checkpoint, upon restart, MongoDB can recover from the last valid checkpoint.
</p>

<p style="text-align: justify;">
  <strong>Compression</strong> &#8211; since WiredTiger has it&#8217;s own cache and since the data in WT Cache doesn&#8217;t&#8217; have to be in the same format as in FS Cache, WF allows 3 levels of compression:
</p>

<li style="text-align: justify;">
  Snappy (default) &#8211; fast
</li>
<li style="text-align: justify;">
  zlib &#8211; more compression
</li>
<li style="text-align: justify;">
  none
</li>

Additional links:

  1. <a href="https://docs.mongodb.org/manual/storage/" target="_blank">https://docs.mongodb.org/manual/storage/</a>
  2. <a href="http://www.wiredtiger.com/" target="_blank">http://www.wiredtiger.com/</a>

&nbsp;

&nbsp;

<p style="text-align: justify;">