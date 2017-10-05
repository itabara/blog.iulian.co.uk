---
title: MongoDB – Document structure and relations
author: Iulian
type: post
date: 2015-12-31T07:39:32+00:00
url: /2015/12/mongodb-document-structure-and-relations/
categories:
  - NoSQL
tags:
  - mongodb

---
## Document structure:

<p style="text-align: justify;">
  MongoDB will represent relationships between documents with:
</p>

<li style="text-align: justify;">
  Embedded data &#8211; The whole document can be retrieved in a single query like this. The drawback is that if the embedded document keeps on growing too much in size, it can impact the read/write performance. The document max size is 16MB.
</li>
<li style="text-align: justify;">
  Manual References &#8211; similar with FK in RDBMS. It is the approach of designing normalized relationship.
</li>
<li style="text-align: justify;">
  Database References &#8211; a document references documents from many collections.
</li>

<p style="text-align: justify;">
  Below some examples with each type of representation applied to different relations.
</p>

## One To One Relations:

<pre class="lang:js decode:true">// embeded document into main document
db.customerInfo.insert(
    {
        firstName:'Iulian',
        lastName:'Tabara',        
        url:'http://www.iuliantabara.com',
        address:{	// let's embed
        	street:'XXXXX Str.',
        	city:'Iasi',
        	country:'Romania'
        }
    }   
);</pre>

<pre class="lang:js decode:true">// OneToOne-Reference.js
db.customerInfo.insert(
    {
    	_id: 1001,
        firstName:'Emilian',
        lastName:'Tabara',        
        url:'http://www.iuliantabara.com',        
    }   
);

db.addressInfo.insert(
    {
    	cust_id: 1001,
       	street:'AAAAAA Str.',
       	city:'Iasi',
       	country:'Romania'
    }
);</pre>

## One to Many relations:

<pre class="lang:js decode:true ">// embeded document into main document (when many is actually few)
db.orderInfo.insert(
    {
        cust_id:101,
        comments: 'My order with orderItems',
        orderItems:[{
            productName: 'Mouse',
            quantity: 1,
        },{
            productName: 'Keyboard',
            quantity: 2,

        },{
            productName: 'CPU',
            quantity: 1,
        }]
    }   
);</pre>

<pre class="lang:js decode:true">// reference orderId into orderItem (when many is large)
db.orderInfo.insert(
    {
        _id: 1,
        cust_id:101,
        comments: 'My second order with orderItems'        
    }   
)

db.orderItemsInfo.insert(    
    {
        order_id: 1,
        productName: 'HDD',
        quantity: 2,
    }
)

db.orderItemsInfo.insert(
    {
        order_id: 1,
        productName: 'SSD',
        quantity: 2,
    }
)

db.orderItemsInfo.insert(
    {
        order_id: 1,
        productName: 'MB',
        quantity: 1,
    }
)

db.orderItemsInfo.insert(
    {
        order_id: 1,
        productName: 'Case',
        quantity: 1,
    }
)</pre>

## Many to Many relations:

<pre class="lang:js decode:true ">// Many to Many - Two Way Embedding
// reference Author and Articles
db.authorInfo.insert(
    {
        id: 101,
        name: 'Iulian Tabara',
        articles:[1,2]
    }
)

db.authorInfo.insert(
    {
        id: 102,
        name: 'Emilian Tabara',
        articles:[2]
    }
)

db.articleInfo.insert(
	{
		id:1,
		title:"MongoDB Design",
		categories:["NoSQL"],
		authors:[101,102]
	}
)

db.articleInfo.insert(
	{
		id:2,
		title:"SQL Tunning",
		categories:["SQL"],
		authors:[102]
	}
)</pre>

<p style="text-align: justify;">
  Note: It&#8217;s recommended to use multiple key indexes (<strong><a href="https://docs.mongodb.org/manual/core/index-compound/" target="_blank">compound index</a></strong>) to make queries against embedded documents efficient.
</p>

<pre class="lang:js decode:true">db.articleInfo.ensureIndex({'authors': 1})

db.articleInfo.find({'authors':{$all: [101,102]}}) -- contains both 101 and 102

db.articleInfo.find({'authors':{$all: [101,102]}}).explain() -- see the index

</pre>

## Benefits of Embedding:

  * Improvement read performance
  * One round trip to the database

## Database References:

There are three fields in <a href="https://docs.mongodb.org/manual/reference/database-references/#dbref-explanation" target="_blank">DBRefs</a>:

<li style="text-align: justify;">
  <b>$ref:</b> This field specifies the collection of the referenced document
</li>
<li style="text-align: justify;">
  <b>$id:</b> This field specifies the _id field of the referenced document
</li>
<li style="text-align: justify;">
  <b>$db:</b> This is an optional field and contains name of the database in which the referenced document lies
</li>

<p style="text-align: justify;">
  Unless you have a compelling reason (if you need to reference documents from multiple collections) to use DBRefs, use manual references instead.
</p>

<pre class="lang:js decode:true">{
   "_id" : "order.1",
   "&lt;strong&gt;custDetails&lt;/strong&gt;": {
      "$ref": "customerName",
      "$id": ObjectId("56868a99a1de4cd4b08a25ab"),
      "$db": "customers"},
   "price": "25",
   "orderDate": "01-01-2015",
   "comments": "My comment"
}</pre>

<p style="text-align: justify;">
  The <strong>custDetails</strong> DBRef field here specifies that the referenced customer document lies in <strong>customerName </strong>collection under <strong>customers </strong>database and has an id of 56868a99a1de4cd4b08a25ab.
</p>

<p style="text-align: justify;">
  The following code dynamically looks in the collection specified by <b>$ref</b> parameter (<b>custDetails</b> in our case) for a document with id as specified by <b>$id</b> parameter in DBRef.
</p>

<pre class="lang:js decode:true">var order = db.customers.findOne("_id": "order.1")
var dbRef = order.custDetails
db[dbRef.$ref].findOne({"_id":(dbRef.$id)})</pre>

######  Additional links:

  1. <a href="https://docs.mongodb.org/manual/applications/data-models-relationships/" target="_blank">https://docs.mongodb.org/manual/applications/data-models-relationships/</a>

&nbsp;