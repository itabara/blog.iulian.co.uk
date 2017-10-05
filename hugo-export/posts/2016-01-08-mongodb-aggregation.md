---
title: MongoDB – Aggregation
author: Iulian
type: post
date: 2016-01-08T00:17:38+00:00
url: /2016/01/mongodb-aggregation/
categories:
  - NoSQL
tags:
  - mongodb

---
<p style="text-align: justify;">
  Aggregation is a tool that transforms and summarizes data. Aggregations operations process data records and return computed results. Aggregation operations group values from multiple documents together, and can perform a variety of operations on the grouped data to return a single result. Aggregation can be used in real-time or cached.
</p>

<p style="text-align: justify;">
  In MongoDB there are 3 modalities for data aggregation:
</p>

<li style="text-align: justify;">
  Single purpose aggregation &#8211; Count, Group, Distinct
</li>
<li style="text-align: justify;">
  Aggregation Pipeline &#8211; single collection, multiple processing steps
</li>
<li style="text-align: justify;">
  MapReduce &#8211; multiple collections, shards, complete flexibility
</li>

<table>
  <tr>
    <td style="text-align: center;">
      <strong>Aggregation</strong>
    </td>
    
    <td style="text-align: center;">
      <strong>Single Purpose Aggregation</strong>
    </td>
    
    <td style="text-align: center;">
      <strong>Aggregation Pipeline</strong>
    </td>
    
    <td style="text-align: center;">
      <strong>MapReduce</strong>
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Complexity</strong>
    </td>
    
    <td style="text-align: center;">
      Low
    </td>
    
    <td style="text-align: center;">
      Medium
    </td>
    
    <td style="text-align: center;">
      High
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Speed</strong>
    </td>
    
    <td style="text-align: center;">
      Very Fast
    </td>
    
    <td style="text-align: center;">
      Medium
    </td>
    
    <td style="text-align: center;">
      Medium / Slow
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Usefulness</strong>
    </td>
    
    <td style="text-align: center;">
      Narrow
    </td>
    
    <td style="text-align: center;">
      Broad
    </td>
    
    <td style="text-align: center;">
      All Inclusive
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Realtime<br /> </strong>
    </td>
    
    <td style="text-align: center;">
      Yes
    </td>
    
    <td style="text-align: center;">
      Yes / limitations
    </td>
    
    <td style="text-align: center;">
      No / Pre-processed
    </td>
  </tr>
</table>

# 1. Single purpose aggregation

Count examples:

<pre class="lang:js decode:true ">db.logs.count()
db.logs.count({action:/view/i}) -- count by action='view' case insensitive
db.logs.count({"owner" : ObjectId("56939a9a91bf86b786841bb6")});
db.logs.count({"owner" : ObjectId("56939a9a91bf86b786841bb6"), action:"VIEW"});</pre>

Distinct examples:

<pre class="lang:js decode:true">db.logs.distinct("action");
db.logs.distinct("request_ip", {owner:ObjectId("56939a9a91bf86b786841bb6")});
db.logs.distinct("request_ip", {owner:ObjectId("56939a9a91bf86b786841bb6"), action:"VIEW"});</pre>

Group By examples:

<pre class="lang:js decode:true">db.logs.group({key:{action:1}, reduce:function(current, result){result.count +=1}, initial:{count:0}}); -- count each action
db.logs.group({key:{owner:1}, reduce:function(current, result){result.count +=1}, initial: {count: 0}}) -- count by object
db.logs.group({key:{owner:1}, cond:{action: "VIEW"}, reduce:function(current, result){result.count +=1}, initial: {count: 0}}) -- count by object with action="VIEW"

</pre>

# 2. Aggregation pipeline

<h6 style="text-align: justify;">
  Pipeline operators are using for each stage of the transformation. Each pipeline operation works on the output from the previous operation and order matters. Some are required to be the first or last in the pipeline.
</h6>

**2.1. $geoNear**

<pre class="lang:js decode:true">// location: longitude: [-180:+180], latitude: [-90:+90]
db.logs.ensureIndex({loc: "2dsphere"})

db.logs.aggregate([
  {
    $geoNear: {
      near: {
        type: "Point",
        coordinates: [-20.43,28.52]
      },
      distanceField: "dist.calculated",
      limit: 5,
      includeLocs: "dist.location",
      spherical: true
    }
  }
]).pretty()
</pre>

The fields &#8220;dist.calculated&#8221; and &#8220;dist.location&#8221; are merged into output result.

<pre class="lang:js decode:true">{
        "_id" : 0,
        "request_ip" : "70.248.232.73",
        "owner" : ObjectId("56939a9a91bf86b786841bb6"),
        "request_date" : "Mon Jan 11 2016 15:38:26 GMT+0200 (GTB Standard Time)",
        "request_method" : "PUT",
        "request_uri" : "iuliantabara.composts\u0000",
        "action" : "VIEW",
        "request_time_milliseconds" : 291,
        "loc" : [
                47.34,
                27.36
        ],
        "cookies" : [
                "session",
                "guest",
                "lastpage"
        ],
        "dist" : {
                "calculated" : 6570593.391022804,
                "location" : [
                        47.34,
                        27.36
                ]
        }
}</pre>

2.2. **$group**

<pre class="lang:js decode:true">db.logs.aggregate([
  {
    $group: {
      _id: {action: "$action"},
      min_request_time: {$min: "$request_time_milliseconds"},
      average_request_time: {$avg: "$request_time_milliseconds"},
      count: {"$sum": 1}
    }
  }
])
</pre>

Note: $action, $request\_time\_milliseconds, $request\_time\_milliseconds &#8211; the value of the field.

You can add $$field to differentiate between a projected field and an existing one: we projected &#8220;round&#8221; field and we refer to it on condition: $$round.raised_amount = the value of the projected field: $round.

<pre class="lang:c# decode:true">db.companies.aggregate([
    { $match: {"funding_rounds.investments.financial_org.permalink": "greylock" } },
    { $project: {
        _id: 0,
        name: 1,
        founded_year: 1,
        rounds: { $filter: {
            input: "$funding_rounds",
            <strong>as: "round"</strong>,
            cond: { $gte: ["<strong>$$round</strong>.raised_amount", 100000000] } } }
    } },
    { $match: {"rounds.investments.financial_org.permalink": "greylock" } },    
]).pretty()</pre>

<pre class="lang:js decode:true">db.companies.aggregate([
    { $match: { "founded_year": 2010 } },
    { $project: {
        _id: 0,
        name: 1,
        founded_year: 1,
        first_round: { $arrayElemAt: [ "$funding_rounds", 0 ] }, -- first element
        last_round: { $arrayElemAt: [ "$funding_rounds", -1 ] } -- last element from $funding_rounds
    } }
]).pretty()</pre>

<pre class="lang:js decode:true ">db.companies.aggregate([
    { $match: { "founded_year": 2010 } },
    { $project: {
        _id: 0,
        name: 1,
        founded_year: 1,
        early_rounds: { $slice: [ "$funding_rounds", 1, 3 ] } -- skip first, consider next 3
    } }
]).pretty()
</pre>

<pre class="lang:js decode:true ">db.companies.aggregate([
    { $match: { "founded_year": 2010 } },
    { $project: {
        _id: 0,
        name: 1,
        founded_year: 1,
        first_round: { $slice: [ "$funding_rounds", 1 ] },
        last_round: { $slice: [ "$funding_rounds", -1 ] }
    } }
]).pretty()</pre>

<pre class="lang:js decode:true ">db.companies.aggregate([
    { $match: { "founded_year": 2004 } },
    { $project: {
        _id: 0,
        name: 1,
        founded_year: 1,
        total_rounds: { $size: "$funding_rounds" }
    } }
]).pretty()</pre>

2.3. **$limit**

<pre class="lang:js decode:true">db.logs.aggregate([{$limit: 2}]).pretty()
</pre>

2.4. **$match**

<pre class="lang:js decode:true"># match (only for specific owner)
 db.logs.aggregate([{$match: {owner: ObjectId("5693845f58b968294575d18a")}}]).pretty()
</pre>

2.5. **$project**

<pre class="lang:js decode:true "># project (reshape document)
db.logs.aggregate([
  {
    $project: {
      _id: "$_id",
      user_action: "$action",
      duration_ms: "$request_time_milliseconds"
    }
  }
]).pretty()
</pre>

The output:

<pre class="lang:js decode:true ">{ "_id" : 0, "user_action" : "VIEW", "duration_ms" : 291 }
{ "_id" : 1, "user_action" : "VIEW", "duration_ms" : 283 }
{ "_id" : 2, "user_action" : "DOWNLOAD", "duration_ms" : 145 }
{ "_id" : 3, "user_action" : "DOWNLOAD", "duration_ms" : 71 }
{ "_id" : 4, "user_action" : "DOWNLOAD", "duration_ms" : 0 }
{ "_id" : 5, "user_action" : "VIEW", "duration_ms" : 245 }
{ "_id" : 6, "user_action" : "DOWNLOAD", "duration_ms" : 73 }
{ "_id" : 7, "user_action" : "VIEW", "duration_ms" : 20 }
{ "_id" : 8, "user_action" : "DOWNLOAD", "duration_ms" : 183 }
{ "_id" : 9, "user_action" : "VIEW", "duration_ms" : 131 }
{ "_id" : 10, "user_action" : "DOWNLOAD", "duration_ms" : 104 }
{ "_id" : 11, "user_action" : "DOWNLOAD", "duration_ms" : 311 }
{ "_id" : 12, "user_action" : "VIEW", "duration_ms" : 260 }
{ "_id" : 13, "user_action" : "VIEW", "duration_ms" : 213 }
{ "_id" : 14, "user_action" : "DOWNLOAD", "duration_ms" : 81 }
{ "_id" : 15, "user_action" : "DOWNLOAD", "duration_ms" : 263 }
{ "_id" : 16, "user_action" : "VIEW", "duration_ms" : 54 }
{ "_id" : 17, "user_action" : "VIEW", "duration_ms" : 76 }
{ "_id" : 18, "user_action" : "VIEW", "duration_ms" : 171 }
{ "_id" : 19, "user_action" : "DOWNLOAD", "duration_ms" : 231 }</pre>

2.6. **$sort**

<pre class="lang:js decode:true">db.logs.aggregate([{$sort: {request_date: -1}}]).pretty()</pre>

2.7. **$unwind **&#8211; explodes document into multiple documents by creating a document for each element into array.

<pre class="lang:js decode:true"># unwind
db.logs.aggregate([{$unwind: "$cookies"}]).pretty()</pre>

The partial output:

<pre class="lang:js decode:true">{
        "_id" : 6,
        "request_ip" : "131.232.212.115",
        "owner" : ObjectId("56939a9a91bf86b786841bb6"),
        "request_date" : "Mon Jan 11 2016 15:38:26 GMT+0200 (GTB Standard Time)",
        "request_method" : "PUT",
        "request_uri" : "iuliantabara.composts\u0006",
        "action" : "DOWNLOAD",
        "request_time_milliseconds" : 73,
        "loc" : [
                40.75,
                -73.99
        ],
        "cookies" : "guest"
}</pre>

You can combine multiple stages as example below:

<pre class="lang:js decode:true"># multiple stage example
db.logs.aggregate([
  {
    $project: {
      _id: "$_id",
      user_action: "$action",
      duration_ms: "$request_time_milliseconds"
    }
  },
  {
    $group: {
      _id: {action: "$user_action"},
      min_duration: {$min: "$duration_ms"},
      avg_duration: {$avg: "$duration_ms"},
      count: {"$sum": 1}
    }
  }
]).pretty()</pre>

<pre class="lang:js decode:true ">db.logs.aggregate([{$match:{user_action:"GET"}},{$sort:{user_id:1}},{$skip:10},{$limit:20},{$project:{_id:0, user_action:1, duration_ms:1}}])</pre>

<pre class="lang:js decode:true">db.companies.aggregate([
    { $match: { "funding_rounds": { $exists: true, $ne: [ ]} } },
    { $project: {
        _id: 0,
        name: 1,
        largest_round: { $max: "$funding_rounds.raised_amount" }
    } }
])</pre>

<pre class="lang:js decode:true ">db.companies.aggregate([
    { $match: { "funding_rounds": { $exists: true, $ne: [ ]} } },
    { $project: {
        _id: 0,
        name: 1,
        total_funding: { $sum: "$funding_rounds.raised_amount" }
    } }
])</pre>

&nbsp;

In the <a href="http://www.iuliantabara.com/2016/01/mongodb-map-reduce/" target="_blank">next </a>article I&#8217;m going to touch another option for data aggregation: MapReduce

References:

<a href="https://docs.mongodb.org/manual/core/aggregation-introduction/" target="_blank">1.https://docs.mongodb.org/manual/core/aggregation-introduction/</a>

<a href="https://docs.mongodb.org/manual/reference/operator/aggregation/" target="_blank">2.https://docs.mongodb.org/manual/reference/operator/aggregation/</a>