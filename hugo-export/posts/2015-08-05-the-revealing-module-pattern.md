---
title: The Revealing Module Pattern
author: Iulian
type: post
date: 2015-08-05T11:00:56+00:00
url: /2015/08/the-revealing-module-pattern/
categories:
  - Javascript

---
<p style="text-align: justify;">
  This pattern is the same concept as the <a href="http://www.iuliantabara.com/2015/08/the-module-pattern/">module pattern</a> in that it focuses on public & private methods. The only difference is that the revealing module pattern was engineered as a way to ensure that all methods and variables are kept private until they are explicitly exposed; usually through an object literal returned by the closure from which it&#8217;s defined.
</p>

## Advantages

  * Cleaner approach for developers
<li style="text-align: justify;">
  Supports private data
</li>
<li style="text-align: justify;">
  Less clutter in the global namespace
</li>
<li style="text-align: justify;">
  Localization of functions and variables through closures
</li>
<li style="text-align: justify;">
  The syntax of our scripts are even more consistent
</li>
<li style="text-align: justify;">
  Explicitly defined public methods and variables which lead to increased readability
</li>

## Disadvantages

  * The same as Module Pattern

<pre class="lang:js decode:true  ">var MyModule = ( function( window, undefined ) {
  
  function myMethod() {
    alert( 'my method' );
  }
  
  function myOtherMethod() {
    alert( 'my other method' );
  }
  
  // explicitly return public methods when this object is instantiated
  return {
    someMethod : myMethod,
    someOtherMethod : myOtherMethod
  };
  
} )( window );

//  example usage
MyModule.myMethod(); // undefined
MyModule.myOtherMethod(); // undefined
MyModule.someMethod(); // alerts "my method"
MyModule.someOtherMethod(); // alerts "my other method"</pre>

&nbsp;