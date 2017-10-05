---
title: 'Private: Javascript – call, apply, bind'
author: Iulian
type: post
date: 2015-07-09T19:52:36+00:00
draft: true
private: true
url: /2015/07/javascript-call-apply-bind/
categories:
  - Javascript

---
<pre class="lang:js decode:true  ">// context = this

var obj = {
	foo: function(one, two, three){
		//console.log(this === window);
		//console.log(three);
		console.log(this);
	}
}

// call, apply, bind
//obj.foo()
//obj.foo.call(window, 1,2,3,4,5) -- change the context
//obj.foo.apply(window,[1,2,3]) -- change the context
var myBoundFoo = obj.foo.bind(window)
myBoundFoo(1,2,3);

$('button').on('click', function(){
	console.log(this);
})</pre>

&nbsp;