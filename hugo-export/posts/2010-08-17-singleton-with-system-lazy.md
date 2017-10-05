---
title: Singleton with System.Lazy
author: Iulian
type: post
date: 2010-08-17T00:11:00+00:00
url: /2010/08/singleton-with-system-lazy/
categories:
  - Design patterns
tags:
  - Lazy

---
<p align="justify">
  A Singleton class is one where there is only ever one instance of the class created. This means that constructors must be private to avoid users creating their own instances, and a static property (or method in languages without properties) is defined that returns a single static instance.
</p>

<pre class="lang:c# decode:true ">public class Singleton
{
    // the single instance is defined in a static field
    private static readonly Singleton _instance = new Singleton();
 
    // constructor private so users can't instantiate on their own
    private Singleton()
    {
    }
 
    // read-only property that returns the static field
    public static Singleton Instance
    {
         get
         {
             return _instance;
         }
    }
}</pre>

This is the most basic singleton, notice the key features:

  1. Static readonly field that contains the one and only instance.
  2. Constructor is private so it can only be called by the class itself.
  3. Static property that returns the single instance.

<p align="justify">
  Looks like it satisfies, right? There’s just one (potential) problem. C# gives you no guarantee of when the static field _instance will be created. This is because the C# standard simply states that classes (which are marked in the IL as BeforeFieldInit) can have their static fields initialized any time before the field is accessed. This means that they may be initialized on first use, they may be initialized at some other time before, you can’t be sure when.
</p>

<p align="justify">
  So what if you want to guarantee your instance is truly lazy. That is, that it is only created on first call to Instance?
</p>

<p align="justify">
  First option is to use lazy construction ourselves, but being that our Singleton may be accessed by many different threads, we’d need to lock it down.
</p>

<pre class="lang:c# decode:true ">public class LazySingleton1
{
    // lock for thread-safety laziness
    private static readonly object _mutex = new object();
 
    // static field to hold single instance
    private static volatile LazySingleton1 _instance = null;
 
    // property that does some locking and then creates on first call
    public static LazySingleton1 Instance
    {
        get
        {
            if (_instance == null)
            {
                lock (_mutex)
                {
                    if (_instance == null)
                    {
                        _instance = new LazySingleton1();
                    }
 
                }
            }
 
            return _instance;
        }
    }
 
    private LazySingleton1()
    {
    }
}</pre>

<p align="justify">
  This is a standard double-check algorithm so that you don’t lock if the instance has already been created. However, because it’s possible two threads can go through the first if at the same time the first time back in, you need to check again after the lock is acquired to avoid creating two instances.
</p>

<p align="justify">
  Second option is to take advantage of the C# standard’s BeforeFieldInit and define your class with a static constructor. It need not have a body, just the presence of the static constructor will remove the BeforeFieldInit attribute on the class and guarantee that no fields are initialized until the first static field, property, or method is called.
</p>

<pre class="lang:c# decode:true ">public class LazySingleton2
{
    // because of the static constructor, this won't get created until first use
    private static readonly LazySingleton2 _instance = new LazySingleton2();
 
    // Returns the singleton instance using lazy-instantiation
    public static LazySingleton2 Instance
    {
        get { return _instance; }
    }
 
    // private to prevent direct instantiation
    private LazySingleton2()
    {
    }
 
    // removes BeforeFieldInit on class so static fields not
    // initialized before they are used
    static LazySingleton2()
    {
    }
}</pre>

<p align="justify">
  Third option is to use .Net 4.0 new features, <strong>System.Lazy</strong> type which guarantees thread-safe lazy-construction. Using <strong>System.Lazy</strong>, we get:
</p>

<pre class="lang:c# decode:true ">public class LazySingleton3
{
    // static holder for instance, need to use lambda to construct since constructor private
    private static readonly Lazy&lt;LazySingleton3&gt; _instance
            = new Lazy&lt;LazySingleton3&gt;(() =&gt; new LazySingleton3());
 
    // private to prevent direct instantiation.
 
    private LazySingleton3()
    {
    }
 
    // accessor for instance
    public static LazySingleton3 Instance
    {
        get
        {
            return _instance.Value;
        }
    }
}</pre>

<p align="justify">
  Note, you need your lambda to call the private constructor as Lazy’s default constructor can only call public constructors of the type passed in (which we can’t have by definition of a Singleton). But, because the lambda is defined inside our type, it has access to the private members so it’s perfect.
</p>

<p align="justify">
  Lazy has many other uses as well, obviously, but I really love how elegant and readable it makes the lazy Singleton.
</p>