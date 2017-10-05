---
title: LINQ built-in performance optimization
author: Iulian
type: post
date: 2009-05-30T20:21:00+00:00
url: /2009/05/linq-built-in-performance-optimization/
categories:
  - Source code
tags:
  - Linq

---
<p align="justify">
  One of the most important concepts in LINQ performance and optimization is <strong>deferred execution</strong>. It simply means that when you declare a variable and assign it a query expression, that expression is not executed immediately.
</p>

<pre class="lang:c# decode:true">// Query is not executed.
var query = from item in storage 
        select item;</pre>

<p align="justify">
  The variable query now stores the command, and the query execution is deferred until you request the actual data from the query. This usually happens either within a <strong>foreach</strong> loop or when you call an aggregate method such as <strong>Min</strong>, <strong>Max</strong>, and <strong>Average</strong>, or when you cache the query results using the <strong>ToList</strong> or <strong>ToArray</strong> methods.
</p>

<pre class="lang:c# decode:true">// foreach loop.
foreach (var item in query)
    Console.WriteLine(item);

// Count method.
int total = query.Count();

// ToArray method.
var cachedQuery = query.ToArray();</pre>

<p align="justify">
  Now let’s look at what else is happening behind the scenes. Does any compiler-level optimization happen during the query execution? The answer is yes. However, there is a catch. From now on we will talk only about queries for <strong>IEnumerable</strong> and <strong>IEnumerable</strong> collections that use the “LINQ to Objects” LINQ provider. For other LINQ providers, including LINQ to SQL and LINQ to XML, different optimization rules might apply.
</p>

<p align="justify">
  Note: <em>It is often believed that because of deferred execution it takes longer to execute a query for the first time. However, in the case of LINQ to Objects queries, there is no difference between the first execution and subsequent ones. With other LINQ providers the rules might be different (for example, there might be some caching going on), but you need to refer to the particular provider’s documentation for details</em>.
</p>

The LINQ to Objects queries are optimized in the following cases:

<p align="justify">
  Some method calls are optimized if the data source implements a necessary interface. The following table lists these optimizations.
</p>

<table cellspacing="0" cellpadding="2" border="1">
  <tr>
    <td valign="top">
      <b></p> 
      
      <h5>
        <b>LINQ method</b>
      </h5>
      
      <p>
        </b></td> 
        
        <td valign="top">
          <b></p> 
          
          <h5>
            <b>Optimization</b>
          </h5>
          
          <p>
            </b></td> </tr> 
            
            <tr>
              <td valign="top">
                Cast
              </td>
              
              <td valign="top">
                If the data source already implements <strong>IEnumerable<T></strong> for the given <strong>T</strong>, when the sequence of data is returned without a cast.
              </td>
            </tr>
            
            <tr>
              <td valign="top">
                Contains
              </td>
              
              <td valign="top" rowspan="2">
                <p align="justify">
                  If the data source implements the <strong>ICollection</strong> or <strong>ICollection<T></strong> interface, the corresponding method of the interface is used.
                </p>
              </td>
            </tr>
            
            <tr>
              <td valign="top">
                Count
              </td>
            </tr>
            
            <tr>
              <td valign="top">
                ElementAt
              </td>
              
              <td valign="top" rowspan="7">
                <p align="justify">
                  If the data source implements the <strong>IList</strong> or <strong>IList<T></strong> interface, the interface’s Count method and indexing operations are used.
                </p>
              </td>
            </tr>
            
            <tr>
              <td valign="top">
                First
              </td>
            </tr>
            
            <tr>
              <td valign="top">
                FirstOrDefault
              </td>
            </tr>
            
            <tr>
              <td valign="top">
                Last
              </td>
            </tr>
            
            <tr>
              <td valign="top">
                LastOrDefault
              </td>
            </tr>
            
            <tr>
              <td valign="top">
                Single
              </td>
            </tr>
            
            <tr>
              <td valign="top">
                SingleOrDefault
              </td>
            </tr></tbody> </table> 
            
            <p>
              If there is a sequence of one or more Where operators immediately followed by a sequence of one or more Select operators, the query creates a single <strong>IEnumerable</strong> or<strong>IEnumerable<T></strong>object and generates no intermediate ones.
            </p>
            
            <pre class="lang:c# decode:true ">var query = from item in storage
            where item.Category = "Food"
            where item.Price &lt; 100
            select item;</pre>
            
            <p>
              In this case, only one <strong>IEnumerable</strong> object is be generated for the query.
            </p>
            
            <p align="justify">
              If you query an array or a list, the enumerator from the <strong>IEnumerable</strong> or <strong>IEnumerable<T></strong> <t>interface is not used in foreach loops. Instead, a simple for loop over the length of the array or list is created behind the scenes, and the elements are accessed directly.
            </p>
            
            <p align="justify">
              Furthermore, the where operators are implemented as simple if statements, so no intermediate enumerators or enumerable is created.
            </p>
            
            <p align="justify">
              Once again, other LINQ providers might have their own optimization rules. But the above rules should give you some idea about how LINQ to Objects works.
            </p>
            
            <p>
              More details <a href="http://blogs.msdn.com/b/csharpfaq/archive/2009/01/26/does-the-linq-to-objects-provider-have-built-in-performance-optimization.aspx" target="_blank">here</a>
            </p>