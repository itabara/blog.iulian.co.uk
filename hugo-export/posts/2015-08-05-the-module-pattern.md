---
title: The Module Pattern
author: Iulian
type: post
date: 2015-08-05T10:56:11+00:00
url: /2015/08/the-module-pattern/
categories:
  - Javascript

---
<p style="text-align: justify;">
  Going to <a href="https://carldanley.com/js-module-pattern/" target="_blank">js-module-pattern, </a>the pattern is used to mimic classes in conventional software engineering and focuses on public and private access to methods & variables. The aim is to improve the reduction of globally scoped variables, thus decreasing the chances of collision with other code throughout an application.
</p>

## Advantages

  * Cleaner approach for developers
  * Supports private data
  * Less clutter in the global namespace
  * Localization of functions and variables through closures

## Disadvantages

  * Private methods are unaccessible.
  * Private methods and functions lose extendability since they are unaccessible.

<pre class="lang:js decode:true">(function (window, undefined) {

    // normally variables & functions start with a lowercase letter but with modules, that is not the case.
    // The general tradition is to start them with a capital letter instead.
    function MyModule() {

        // `this` refers to the instance of `MyModule` when created
        this.myMethod = function myMethod() {
            alert('my method');
        };

        // note that we still use a function declaration even when using a function expression.
        // for more information on why, check out: http://kangax.github.io/nfe/
        this.myOtherMethod = function myOtherMethod() {
            alert('my other method');
        };

    }

    // expose access to the constructor
    window.MyModule = MyModule;

})(window);

// example usage
var myModule = new MyModule();
myModule.myMethod(); // alerts "my method"
myModule.myOtherMethod(); // alerts "my other method"</pre>

<p style="text-align: justify;">
   Another example of the module pattern that exposes the module a little differently and makes use of a shared private cache. This method encourages more of an object creation approach where we can optimize performance by being efficient with shared storage.
</p>

<pre class="lang:js decode:true ">var MyModule = ( function( window, undefined ) {

  // this object is used to store private variables and methods across multiple instantiations
  var privates = {};
  
  function MyModule() {
    
    this.myMethod = function myMethod() {
      alert( 'my method' );
    };

    this.myOtherMethod = function myOtherMethod() {
      alert( 'my other method' );
    };

  }

  return MyModule;
  
} )( window );

// example usage
var myMod = new MyModule();
myMod.myMethod(); // alerts "my method"
myMod.myOtherMethod(); // alerts "my other method"</pre>

&nbsp;