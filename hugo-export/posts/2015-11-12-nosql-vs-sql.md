---
title: Nosql vs SQL
author: Iulian
type: post
date: 2015-11-12T15:47:25+00:00
url: /2015/11/nosql-vs-sql/
categories:
  - NoSQL
tags:
  - mongodb
  - NoSQL

---
<h6 style="text-align: justify;">
  Relational databases (RDBMS) were not designed to cope with the scale and agility challenges that face modern applications, nor were they built to take advantage of the commodity storage and processing power available today.
</h6>

<p style="text-align: justify;">
  The Next Generation of Databases is addressing some of the points: being <strong>non-relational, distributed, open-source</strong> and <strong>horizontally scalable</strong>.
</p>

<p style="text-align: justify;">
  The movement began early 2009 and is growing rapidly. Often more characteristics apply such as: <strong>schema-free, easy replication support, simple API, eventually consistent</strong> / <strong>BASE</strong> (not ACID), a <strong>huge amount of data</strong> and more.
</p>

<p style="text-align: justify;">
  Why is NoSQL exploding? The easy way to explain this is flexibility and power. So do we want to drop 40+ years of RDBMS ? No, more than that the community now translates &#8220;nosql&#8221; mostly with &#8220;<strong>not only sql</strong>&#8220;.
</p>

<h6 style="text-align: justify;">
  NoSQL Database Types:
</h6>

<li style="text-align: justify;">
  Key-value stores: <a href="http://redis.io/" target="_blank">Redis</a>, <a href="http://aws.amazon.com/dynamodb/" target="_blank">Dynamo</a>, <a href="http://basho.com/products/" target="_blank">Riak</a>
</li>
<li style="text-align: justify;">
  Family Column-oriented: <a href="https://cloud.google.com/bigtable/" target="_blank">BigTable</a>, <a href="http://cassandra.apache.org/" target="_blank">Cassandra</a>, <a href="https://aws.amazon.com/simpledb/" target="_blank">SimpleDB</a>
</li>
<li style="text-align: justify;">
  Document-oriented: <a href="https://www.mongodb.org/" target="_blank">MongoDB</a>, <a href="http://couchdb.apache.org/" target="_blank">CouchDB</a>
</li>
<li style="text-align: justify;">
  Graph &#8211; <a href="http://orientdb.com/orientdb/" target="_blank">OrientDB</a>, <a href="http://neo4j.com/" target="_blank">Neo4J</a>, <a href="http://thinkaurelius.github.io/titan/" target="_blank">Titan</a>
</li>

<p style="text-align: justify;">
  According to <a href="http://nosql-database.org/" target="_blank">http://nosql-database.org/  </a>there are 225+ NoSQL solutions available.
</p>

<p style="text-align: justify;">
  The NoSQL market is expected to be $3.5 billion by 2018, according to a Market Research Media Ltd. In the meantime the RDBMS market is around $26 billion (n.r. 2013) with about 9 percent annual growth so by 2018 the RDBMS market will reach $40 billion.
</p>

<p style="text-align: justify;">
  In the future posts I&#8217;m going to focus on MongoDB database, covering from basic aspects and DevOps to more advanced topics.
</p>

<p style="text-align: justify;">
  References:
</p>

<li style="text-align: justify;">
  <a href="http://www.wired.com/insights/2013/09/the-future-of-enterprise-data-rdbms-will-be-there/" target="_blank">http://www.wired.com/insights/2013/09/the-future-of-enterprise-data-rdbms-will-be-there/</a>
</li>
<li style="text-align: justify;">
  <a href="http://nosql-database.org/" target="_blank">http://nosql-database.org/</a>
</li>
<li style="text-align: justify;">
  <a href="https://www.mongodb.com/nosql-explained" target="_blank">https://www.mongodb.com/nosql-explained</a>
</li>
<li style="text-align: justify;">
  <a href="http://slamdata.com/blog/2014/07/07/Henry-Ford-NoSQL-data-and-the-future-of-analytics.html" target="_blank">http://slamdata.com/blog/2014/07/07/Henry-Ford-NoSQL-data-and-the-future-of-analytics.html</a>
</li>

<p style="text-align: justify;">