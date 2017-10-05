---
title: MongoDB – Installation and basic commands
author: Iulian
type: post
date: 2015-11-15T11:02:15+00:00
url: /2015/11/mongodb-basic-usage/
categories:
  - NoSQL
tags:
  - mongodb

---
<p style="text-align: justify;">
  <a href="https://www.mongodb.org/" target="_blank">MongoDB </a>is a document-orientated database, a NoSQL database with a JSON-like documents and dynamic schema:
</p>

<li style="text-align: justify;">
  Tables are collections
</li>
  * Rows are documents
  * No schema

## The advantages of using documents:

<li style="text-align: justify;">
  Documents (objects) correspond to native data types in many programming languages.
</li>
<li style="text-align: justify;">
  Embedded documents and arrays reduce need for expensive joins.
</li>
<li style="text-align: justify;">
  Ability to perform dynamic queries on documents using document-based query language.
</li>
<li style="text-align: justify;">
  Conversion of objects to database objects is not needed
</li>
<li style="text-align: justify;">
  High performance &#8211; uses internal memory to shore working sets of quick data access.
</li>

<h2 style="text-align: justify;">
  Advantages of MongoDB vs RDBMS
</h2>

<li style="text-align: justify;">
  <strong>Schema less</strong> &#8211; the document is flexible compared to &#8220;table&#8221; structure including nullable fields from RDBMS. One collection can hold even different different documents. Number of fields, content and size of the document can be differ from one document to another.
</li>
<li style="text-align: justify;">
  <strong>No complex joins</strong> &#8211; as data is usually stored with the same document. We&#8217;re going to discuss about embedding related data in document and how MongoDB is dealing with the lack of relations.
</li>
<li style="text-align: justify;">
  <strong>Ease of scale-out </strong>&#8211; MongoDB is easy to scale.
</li>
<li style="text-align: justify;">
  <strong>Conversion / mapping</strong> of application objects to database objects not needed. See <a href="https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch" target="_blank">https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch.</a>
</li>
<li style="text-align: justify;">
  <strong>Document Oriented Storage:</strong> Data is stored in the form of JSON style documents. Everybody is talking JSON there days: client, server and database.
</li>
<li style="text-align: justify;">
  <strong>Atomic</strong> &#8211; in-place updates: <strong>$inc, $set, $push, $pop, $rename, $bit</strong>. No need to deal with the complexity of transactions.
</li>

## Where should use MongoDB?

<ul class="list">
  <li>
    Big Data
  </li>
  <li>
    Content Management and Delivery
  </li>
  <li>
    Mobile and Social Infrastructure
  </li>
  <li>
    User Data Management
  </li>
  <li>
    Data Hub
  </li>
</ul>

## Installation

<p style="text-align: justify;">
  MongoDB is a cross-platform so major OS are covered together with the option to install manually or via setup procedure (msi, apt-get, <a class="reference external" href="http://brew.sh/" target="_blank">homebrew</a>).
</p>

<p style="text-align: justify;">
  Below we&#8217;re going to add the steps to manually install mongoDB on Windows platform. You can find the latest stable release <a href="https://www.mongodb.org/dl/win32/" target="_blank">here</a>.
</p>

<p style="text-align: justify;">
  For Debian/Ubuntu installation, please use <a href="http://www.iuliantabara.com/2015/12/mongodb-3-2-into-xubuntu-15-10/" target="_blank">this</a> link.
</p>

<p style="text-align: justify;">
  For the installation of mongodb I recommend to use a custom folder (eg.<strong>d:\mongodb)</strong> Also is recommended to open cmd.exe as admin and then create<strong> data\db</strong> and <strong>log</strong> folders.
</p>

<pre class="lang:sh decode:true ">md d:\mongodb\data\db
md d:\mongodb\log

D:\mongodb\bin&gt;mongod --directoryperdb --dbpath d:\mongodb\data\db --logpath d:\mongodb\log\mongodb.log --logappend -rest --install
2015-11-17T11:03:31.855+0200 I CONTROL  ** WARNING: --rest is specified without --httpinterface,
2015-11-17T11:03:31.858+0200 I CONTROL  **          enabling http interface

D:\mongodb\bin&gt;net start MongoDB
The MongoDB service is starting.
The MongoDB service was started successfully.</pre>

<p style="text-align: justify;">
  You could simplity and add some details into <strong>mongo.config</strong> to adjust the config of mongod service:
</p>

<pre class="lang:batch decode:true ">bind_ip = 127.0.0.1  
port = 27017  
quiet = true  
dbpath=e:\mongodb\data\db   
logpath=e:\mongodb\log\mongo.log  
logappend = true   
diaglog=3  
journal = true</pre>

<p style="text-align: justify;">
  For the manually binary installation it&#8217;s better to start the <strong>mongod</strong> as a windows service:
</p>

<pre class="lang:ps decode:true">sc.exe delete MongoDB -- delete if already exists
sc.exe create MongoDB binPath="e:\mongodb\bin\mongod.exe --service --config="E:\mongodb\mongo.config"" DisplayName="MongoDB" start="auto"
sc.exe start MongoDB</pre>

## Mongo Shell

Let&#8217;s start the shell by using **mongo.exe.
  
Mongod.exe** is the mongoDB server.

Below some basic mongodb shell commands:

<pre class="lang:js decode:true ">db - display current db
show dbs - show the list of databases
use &lt;databasename&gt; - switch / creates a database
show collections - display the list of collections
db.createCollection('users') - create collection 'users'
db.users.insert({name: "Iulian Tabara", username:"devuser1", email:"contact@iuliantabara.com"});
db.users.find() - display first 20 documents from the users collection
db.users.find().pretty() - easy to read JSON formatter
db.users.update({username:"devuser1"}, {$set:{email:"devuser1@gmail.com", name:"Iulian Tabără"}}); - without set will keep the fields included in update</pre>

This is the first article on a series of <a href="http://www.iuliantabara.com/tag/mongodb/" target="_blank">mongoDB </a>related ones.

Additional links:

<a href="https://www.mongodb.com/presentations/mongodb-chicago-benefits-using-mongodb-over-rdbms" target="_blank">1.https://www.mongodb.com/presentations/mongodb-chicago-benefits-using-mongodb-over-rdbms</a>

<a href="https://dzone.com/articles/when-use-mongodb-rather-mysql" target="_blank">2.https://dzone.com/articles/when-use-mongodb-rather-mysql</a>