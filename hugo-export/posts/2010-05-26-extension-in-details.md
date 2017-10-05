---
title: 'Extension: ‘In’ Details'
author: Iulian
type: post
date: 2010-05-25T23:54:00+00:00
url: /2010/05/extension-in-details/
categories:
  - Source code
tags:
  - Extension methods

---
<p align="justify">
  Extension methods enable you to “add” methods to existing types without creating a new derived type, recompiling, or otherwise modifying the original type. Extension methods are a special kind of static method, but they are called as if they were instance methods on the extended type.
</p>

Let’s consider the following piece of code:

<pre class="lang:c# decode:true ">if(reallyLongIntegerVariableName == 1 ||
    reallyLongIntegerVariableName == 6 ||
    reallyLongIntegerVariableName == 9 ||
    reallyLongIntegerVariableName == 11)
{
  // do something....
}</pre>

or

<pre class="lang:c# decode:true ">if(reallyLongStringVariableName == "string1" ||
    reallyLongStringVariableName == "string2" ||
    reallyLongStringVariableName == "string3")
{
  // do something....
}</pre>

or

<pre class="lang:c# decode:true ">if(reallyLongMethodParameterName == SomeEnum.Value1 ||
    reallyLongMethodParameterName == SomeEnum.Value2 ||
    reallyLongMethodParameterName == SomeEnum.Value3 ||
    reallyLongMethodParameterName == SomeEnum.Value4)
{
  // do something....
}</pre>

<p align="justify">
  Here is the extension method that determines whether a variable of type T is contained in the supplied list of arguments of type T, allowing for more concise code.
</p>

<pre class="lang:c# decode:true ">public static bool In&lt;T&gt;(this T source, params T[] list)
{
  if(null==source) throw new ArgumentNullException("source");
  return list.Contains(source);
}</pre>

Refactored code:

<pre class="lang:c# decode:true ">if(reallyLongIntegerVariableName.In(1,6,9,11))
{
      // do something....
}</pre>

<pre class="lang:c# decode:true ">if(reallyLongStringVariableName.In("string1","string2","string3"))
{
      // do something....
}</pre>

<pre class="lang:c# decode:true ">if(reallyLongMethodParameterName.In(SomeEnum.Value1, SomeEnum.Value2, SomeEnum.Value3, SomeEnum.Value4)
{
  // do something....
}</pre>

<p align="justify">
  There are some constraints in declaring extension methods. They can be declared by specifying the keyword this as a modifier on the first parameter of the methods. Extension methods can only be declared in non-generic, non-nested static classes.
</p>

<p align="justify">
  Extension methods can be accessed in the namespace in which they are declared or if they are imported with the using directive. If there is any collision between an extension method with an existing non extension method at any given time (collisions occur if the signatures of the methods is identical, excluding the first parameter in the extension methods), the non extension method is invoked. Bellow is a simple example to make things clear:
</p>

<pre class="lang:c# decode:true ">public static class Ext
{
    public static void M(this object obj, string s) { }
}
 
class A { }
 
class B
{
    public void M(string s) { }
}
 
class C
{
    static void Call(A a, B b)
    {
        // call extension method from class Ext
        a.M(“John”);
        // call method from class M
        b.M(“John”);
    }
}</pre>

<p align="justify">
  Extension methods should be used only when the traditional techniques are not feasible. I would recommend the use of the inheritance technique where applicable due to a few reasons. One of them is the fact that extension methods are a bit less intuitive since the code is in a separate static class and not grouped with all the members of a given subclass. Still, they provide an excellent way to extend existing libraries and create Frameworks.
</p>

<p align="justify">
  More information on extension methods and other new features in C# 3.0 can be found on <a title="MSDN" href="http://msdn2.microsoft.com/en-us/library/bb308966.aspx#csharp3.0overview_topic3" target="_blank">MSDN </a>and on <a title="ScottGu's" href="http://weblogs.asp.net/scottgu/archive/2007/03/13/new-orcas-language-feature-extension-methods.aspx" target="_blank">ScottGu’s</a> blog.
</p>