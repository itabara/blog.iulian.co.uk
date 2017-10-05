---
title: using Statement
author: Iulian
type: post
date: 2008-02-01T00:04:00+00:00
url: /2008/02/using-statement/
categories:
  - Design patterns
  - Source code
tags:
  - object adapter

---
The C# ECMA specification states that a using statement: 

<pre class="lang:c# decode:true ">using (type variable = initialization)
{
    embeddedStatement
}</pre>

is exactly equivalent to:

<pre class="lang:c# decode:true ">type variable = initialization;
try
{
    embeddedStatement
}
finally
{
    if (variable != null)
    {
        ((IDisposable)variable).Dispose();
    }
}</pre>

This relies on the **IDisposable **interface from the System namespace:

<pre class="lang:c# decode:true ">namespace System
{
    public interface IDisposable
    {
        void Dispose();
    }
}</pre>

<p align="justify">
  Note that the cast inside the finally block implies that variable must be of a type that supports the <strong>IDisposable </strong>interface (either via inheritance or conversion operator). If it doesn’t you’ll get a compile time error.
</p>

#### Using on classes without **IDisposable**

****

<p align="justify">
  It’s instructive to consider what would happen if class didn’t implement the <strong>IDisposable </strong>interface and you must implement Disposability in our own classes. One solution is the <u>Object Adapter pattern</u>. For example:
</p>

<pre class="lang:c# decode:true ">public sealed class AutoTextReader : IDisposable
{
    private readonly TextReader adaptee;
 
    public AutoTextReader(TextReader target)
    {
        // PreCondition(target != null);
        adaptee = target;
    }
 
    public TextReader TextReader
    {
        get { return adaptee; }
    }
 
    public void Dispose()
    {
        adaptee.Close();
    }   
}</pre>

which you would use like this:

<pre class="lang:c# decode:true ">using (AutoTextReader scoped = new AutoTextReader(file.OpenText()))
{
    scoped.TextReader.Read(source, 0, length);
}</pre>

To make things a little easier you can create an implicit conversion operator:

<pre class="lang:c# decode:true ">&lt;pre class="lang:c# decode:true " &gt;public sealed class AutoTextReader : IDisposable
{
...
    public static implicit operator AutoTextReader(TextReader target)
    {
        return new AutoTextReader(target);
    }
...
}</pre>

which would allow you to write this:

<pre class="lang:c# decode:true ">using (AutoTextReader scoped = file.OpenText())
{
    scoped.TextReader.Read(source, 0, length);
}</pre>