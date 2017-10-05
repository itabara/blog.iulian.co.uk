---
title: Node.JS Module Patterns
author: Iulian
type: post
date: 2016-08-15T16:18:47+00:00
url: /2016/08/node-js-module-patterns/
categories:
  - Node.JS

---
# **1. The simplest module**

<pre class="lang:js decode:true ">// helloWorld.js
console.log(‘Hello World’);

// app.js
var helloWorld = require(‘helloWorld.js');</pre>

# **2.Module Patterns**
  
**2.1. Define a global**

<pre class="lang:js decode:true">// my_module.js
my_module = function(){
  console.log(‘my module’);
};

// app.js
require(‘./my_module.js’);
my_module();

</pre>

> ## <em style="font-weight: 300; color: #333333; line-height: 1.625;">Node: Don’t pollute the global space</em>

## **2.2. Export an anonymous function**

<pre class="lang:js decode:true">// my_module.js 
module.exports = function(){
  console.log(‘my module’);
};

// app.js 
var my_module = require(‘./my_module’);
my_module();</pre>

## **2.3. Export a named function**

<pre class="lang:js decode:true ">// my_module.js 
exports.foo = function(){
  console.log(‘my module’);
};

// app.js 
var foo = require(‘./my_module’).foo;
foo();</pre>

## **2.4. Export an anonymous object**

<pre class="lang:js decode:true">// my_module.js 
var Foo = function(){};
Foo.prototype.log = function(){
  console.log(‘my module’);
};
module.exports = new Foo();

// app.js 
var foo = require(‘./my_module’);
foo.log();</pre>

## **2.5. Export a named object
  
**

<pre class="lang:js decode:true ">// my_module.js 
var Foo = function(){};
Foo.prototype.log = function(){
  console.log(‘my module’);
};
exports.Foo = new Foo();

// app.js 
var foo = require(‘./my_module’).Foo;
foo.log();</pre>

## **2.6. Export an anonymous prototype**

<pre class="lang:js decode:true ">// my_module.js 
var Foo = function(){};
Foo.prototype.log = function(){
  console.log(‘my module’);
};
module.exports = Foo;

// app.js 
var Foo = require(‘./my_module’);
var foo = new Foo();
foo.log();</pre>

## **2.7. Export a named prototype**

<pre class="lang:js decode:true">// my_module.js 
var Foo = function(){};
Foo.prototype.log = function(){
  console.log(‘my module’);
};
exports.Foo = Foo;

// app.js 
var Foo = require(‘./my_module’).Foo;
var foo = new Foo();
foo.log();</pre>

# 3.Summary:

  * Named exports &#8211; allow you to export multiple ‘things’ from a single module
  * Anonymous exports &#8211; simpler client interface

http://www.commonjs.org/specs/modules/1.0/