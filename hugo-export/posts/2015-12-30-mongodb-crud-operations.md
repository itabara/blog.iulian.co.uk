---
title: MongoDB – CRUD Operations
author: Iulian
type: post
date: 2015-12-30T09:11:00+00:00
url: /2015/12/mongodb-crud-operations/
categories:
  - NoSQL
tags:
  - mongodb

---
**MongoDB** is a cross-platform, document oriented database that provides, high performance, high availability, and easy scalability.

## Quick Command reference:

<pre>mongod --config e:\mongodb\mongo.config --journal
mongod --auth --dbpath e:\mongodb\data\db\ -- if you want authentication

use &lt;database&gt; -- switch or create the database
show dbs
db -- show current database
db.getName() -- the same as above
show collections
load("&lt;fullpath_json file");
db.getLastError() -- displays last error</pre>

## CRUD operations

## Creating documents:

MongoDB is able to provide few options to import documents into collection: <a href="https://docs.mongodb.org/manual/reference/method/db.collection.insertOne/#db.collection.insertOne" target="_blank">insertOne</a>, <a href="https://docs.mongodb.org/manual/reference/method/db.collection.insert/#db.collection.insert" target="_blank">insert</a>, <a href="https://docs.mongodb.org/manual/reference/method/db.collection.update/#db.collection.update" target="_blank">update (upsert)</a> and <a href="https://docs.mongodb.org/manual/reference/method/db.collection.save/#db.collection.save" target="_blank">save</a>.

<pre>db.moviesScratch.insertOne({ "firstName": "Iulian", "lastName":"Tabara", "year": "1971"});</pre>

output:

<pre>{
        "acknowledged" : true,
        "insertedId" : ObjectId("5699295ed081b02bef414f03")
}</pre>

  * A boolean acknowledged as true if the operation ran with [_write concern_][1] or false if write concern was disabled.

If you want to add multiple documents at once, MongoDB provides <a href="https://docs.mongodb.org/manual/reference/method/db.collection.insertMany/#db.collection.insertMany" target="_blank">insertMany </a>with _ordered_ (default &#8211; it will stop at first error) or _unordered_ mode &#8211; even there are errors MongoDB will continue the insert with the next document.

<pre>db.books.insertMany(
    [
        {
	    "_id" : "001",
	    "title" : "Book 1",
	    "year" : 1981	    
        },
        {
	    "_id" : "002",
	    "title" : "Book 2",
	    "year" : 1986	    
        },
        {
	    "_id" : "001",
	    "title" : "Book X",
	    "year" : 1983	    
        },
        {
	    "_id" : "003",
	    "title" : "Book 3",
	    "year" : 1983   
        }
    ],
    {
        "ordered": false 
    }
);
</pre>

output:

\[code language=&#8221;javascript&#8221;\]\[/code\]

Update with upsert = true

If set to true, creates a new document when no document matches the query criteria. The default value is false, which does _not_ insert a new document when no match is found.

You can use **save** command to quick update/insert a document into collection:

<pre>// existing object -&gt; update
myobj = db.sample.findOne()
myobj.newProperty = 33
db.sample.save(myobj)

// new object -&gt; insert
objtmp = {x: "NewObjecT"}
db.sample.save(objtmp)</pre>

## Reading documents:

<pre>db.studentsInfoCollection.find({"age":{$eq:12}}).pretty();
db.studentsInfoCollection.find({"name.lastName":{$eq:"Tabara"}}).pretty();
db.studentsInfoCollection.find({"age":{$lt:20}}).pretty();
db.studentsInfoCollection.find({"age":{$gt:15}}).pretty();
db.studentsInfoCollection.find({"subjects":{$in:["Small Business"]}}).pretty();
db.studentsInfoCollection.find({"subjects":{$exists:true, $nin:["Small Business"]}}).pretty();
</pre>

<pre>db.studentsInfoCollection.find({}, {"name.lastName" : 1,"_id":0}); -- retrieve only specific fields, 1 to show, 0 to hide
db.studentsInfoCollection.find({},{"subjects": {$slice:[2,1]}}).pretty(); -- retrieves from subjects only 1 values, starting with offset 2 -&gt; $slice:[2,1]

db.studentsInfoCollection.find({'age': {$gt:11}},{"name.firstName":1, "age":1}); -- projection and retrieves only name.firstName and age.
</pre>

## Cursors:

<pre>var cursor = db.studentsInfoCollection.find();
var document = function(){return cursor.hasNext() ? cursor.next() : null;}
doc(); -- iterate in the batch</pre>

**cursor.objsLeftInBatch()** returns the number of objects remaining in batch.

## Logical operators:

<pre>db.studentInfoCollection.find({ _id: { $type: "string" } })

db.studentInfoCollection.find({ $or : [ { "age": { $gt: 19 } },
                               { "finalMark": { $gt: 95 } } ] })


db.studentInfoCollection.find({ $and : [ { "name.firstName": { $ne: "Iulian" } },
                                { "courses" { $exists: true } } ] })</pre>

## Array match:

<pre>db.studentsInfoCollection.find({"courses":["Math","Chemistry"]}).count() --exact match, order matters

db.studentsInfoCollection.find({"courses":"Chemistry"}).count() --courses array contains Chemistry value

db.studentsInfoCollection.find({"courses.0":"Math"}).count() -- first position from courses arrray is Math</pre>

## Update operators:

<pre>db.collection.update( query_document , update_document , [ options_document ] )</pre>

where optional options_document has any one or more of the following optional parameters:

<pre>upsert : true/false,
multi : true/false, 
writeConcern: document</pre>

Example:

<pre>db.studentsInfoCollection.update({"name.firstName":"Iulian"}, {$set:{"age":35}});

db.studentsInfoCollection.update({"name.firstName":"Iulian"}, {$set:{"age":35}}, {upsert:true}); -- if not found, it create it

db.studentsInfoCollection.update({"name.firstName":"Iulian"}, {$set:{"subjects.1":"MongoDB"}}); -- second element from subjects array

db.sample.update({_id:20}, {$set: {x:"Hi Iulian"}}); -- set value of x even it doesn't exists

db.sample.update({_id:20}, {$inc: {f:1}}); -- increase the value of f

db.sample.update({_id:20}, {$push: {arr:"hi"}}); -- add value "hi" to arr arrray into the document

db.sample.update({_id:20}, {$addToSet: {arr:"bye"}}); -- add to array if not already in array values</pre>

See <a href="https://docs.mongodb.org/manual/reference/operator/update/" target="_blank">https://docs.mongodb.org/manual/reference/operator/update/</a>

## Delete:

<pre>db.studentsInfoCollection.remove({"name.firstName":"Tabara"});
db.studentsInfoCollection.remove({"subjects":"Maths"}, 1); -- removes just one documents if multiple returned</pre>

# Data aggregation:

<pre>db.blogPosts.aggregate([{$group: {_id:"$author", total_posts:{$sum:1}}}]); -- total_posts = total_posts+1

db.blogPosts.distinct("tags");

db.blogPosts.count();

db.blogPosts.find({"tags": "documents"}).count();

db.blogPosts.find().sort({"likes": -1}); --"1" for ascending, "-1" for descending

db.blogPosts.find().sort({$natural:1}); -- the order in which the database refers to documents on disk. Not the insertion order as updated documents are at last</pre>

###### Please follow [this ][2]link with additional info about Data Aggregation.

###### 

# Users management:

<pre>db.getUsers()

db.createUser({user:"iulian", pwd:"iulian", roles:[{role: "userAdmin", db:"customers"}]}); -- create user with role and assign to db

use customers

db.auth("iulian", "iulian");</pre>

# Reduce function:

<pre>db.orders.insert({"Customer": "X", "Items":{"a":20, "b":80}});
db.orders.insert({"Customer": "Y", "Items":{"a":40, "b":60}});

db.orders.group({key:{"Customer":1}, reduce:function(curr, res){res.total += curr.Items.a + curr.Items.b;}, initial:{total:0}});</pre>

and the output:

<pre>[ db.orders.group({key:{"Customer":1}, reduce:function(curr, res){res.total += curr.Items.a + curr.Items.b;}, initial:{total:0}});
        {
                "Customer" : "X",
                "total" : 100
        },
        {
                "Customer" : "Y",
                "total" : 100
        }
]</pre>

Pattern matching with $regex = equivalent to &#8220;like %&#8221; in RDBMS

<pre>db.studentsInfoCollection.find({"name.firstName":{&lt;strong&gt;$regex:"Doina"}&lt;/strong&gt;}).pretty(); -- contains "Doina"

db.studentsInfoCollection.find({"name.firstName":/Doina/}).pretty(); -- equivalent to above

db.studentsInfoCollection.find({"name.firstName":{$regex:/^E/}}).pretty(); -- firstName start with E

db.studentsInfoCollection.find({"name.firstName":{$regex:/n$/}}).pretty(); -- Ends with "n"</pre>

Pattern matching without $regex:

<pre>db.studentsInfoCollection.find({"name.firstName":/n$/}).pretty();

db.studentsInfoCollection.find({"name.firstName":/^D/}).pretty();

db.studentsInfoCollection.find({"name.firstName":{$regex:/N$/, $options:'i'}}).pretty(); -- ignore case for "N"

db.studentsInfoCollection.find({"name.firstName":/^D/i}).pretty(); -- ignore case for D

db.studentsInfoCollection.find({"name.lastName":{$regex:/^t/, $options:'i'}}).sort({$natural: -1}).limit(4); -- last &lt;limit&gt; documents starting with "T"

</pre>

# Map-Reduce:

Map-Reduce is a data processing paradigm for condensing large volumes of data into useful aggregated results.

<pre>db.deposit.insert({cust_id:1, credit:100, debit:20, status: "Verified"});
db.deposit.insert({cust_id:1, credit:500, debit:120, status: "Verified"});
db.deposit.insert({cust_id:2, credit:250, debit:0, status: "Verified"});
db.deposit.insert({cust_id:2, credit:750, debit:100, status: "Verified"});
db.deposit.insert({cust_id:1, credit:1500, debit:400, status: "Verified"});
db.deposit.insert({cust_id:1, credit:3000, debit:1500, status: "Pending"});
db.deposit.insert({cust_id:2, credit:1000, debit:500, status: "Pending"});
db.deposit.insert({cust_id:1, credit:2000, debit:300, status: "Verified"});

db.deposit.mapReduce(
    function(){emit(this.cust_id, this.credit);},
    function(key, values){ return Array.sum(values)},
    {
        query:{status:"Verified"},
        out: "TotalCredit"
    }
);

db.TotalCredit.find();

db.deposit.mapReduce(
    function(){emit(this.cust_id, this.debit);},
    function(key, values){ return Array.sum(values)},
    {
        query:{status:"Verified"},
        out: "TotalCredit"
    }
).find();</pre>

[<img src="http://www.iuliantabara.com/wp-content/uploads/2015/12/map-reduce.png" alt="map-reduce" align="middle" class="aligncenter" />][3]

Source: <a href="https://docs.mongodb.org/manual/core/map-reduce/" target="_blank">mongodb</a>

Additional links:<a href="http://www.iuliantabara.com/2015/12/mongodb-use/referencecards15-pdf/" rel="attachment wp-att-794">1.MongoDB card reference.pdf</a>

 [1]: https://docs.mongodb.org/manual/reference/glossary/#term-write-concern
 [2]: http://www.iuliantabara.com/2016/01/mongodb-aggregation/
 [3]: http://www.iuliantabara.com/wp-content/uploads/2015/12/map-reduce.png