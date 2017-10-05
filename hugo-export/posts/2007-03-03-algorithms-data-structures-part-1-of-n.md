---
title: 'Algorithms & Data Structures – Part 1 of N'
author: Iulian
type: post
date: 2007-03-03T13:48:32+00:00
url: /2007/03/algorithms-data-structures-part-1-of-n/
categories:
  - Algorithms
tags:
  - algorithms
  - complexity

---
## Introduction

<p style="text-align: justify;">
  When we create/analyse an algorithm, we should keep in mind at least three aspects:
</p>

<li style="text-align: justify;">
  What we&#8217;re trying to achieve &#8211; formalize
</li>
<li style="text-align: justify;">
  Correctness
</li>
<li style="text-align: justify;">
  Efficiency (time, memory usage, power consumption)
</li>

How to determine how much time an algorithm A uses to solve problem X ?

  * Depends on input (input size &#8220;n&#8221; as parameter)
  * Determine function f(n) representing cost.

<p style="text-align: justify;">
  <strong>Empirical analysis:</strong> code is and use timer running on many inputs.
</p>

<p style="text-align: justify;">
  <strong>Algorithm analysis:</strong> analyze steps of algorithm, estimating amount of work each step takes.
</p>

## Big O Notation

<p style="text-align: justify;">
  In theoretical analysis of algorithms it is common to estimate their complexity in the asymptotic sense, i.e., to estimate the complexity function for arbitrarily large input. <a style="font-weight: 300;" title="Big O notation" href="https://en.wikipedia.org/wiki/Big_O_notation">Big O notation</a><span style="font-weight: 300;">, </span><a class="mw-redirect" style="font-weight: 300;" title="Big-omega notation" href="https://en.wikipedia.org/wiki/Big-omega_notation">Big-omega notation</a><span style="font-weight: 300;"> and </span><a class="mw-redirect" style="font-weight: 300;" title="Big-theta notation" href="https://en.wikipedia.org/wiki/Big-theta_notation">Big-theta notation</a><span style="font-weight: 300;"> are used to this end.</span>
</p>

<p style="text-align: justify;">
  <strong>Rate of growth:</strong> measure how quickly the graph of function rises.
</p>

<li style="text-align: justify;">
  <strong>T(n) = O(f(n))</strong> <em>(big-oh)</em> means that the growth rate of <strong>T(n)</strong> is asymptotically <strong>less than or equal to</strong> to the growth rate of <strong>f(n)</strong>. <em>Lingo: T(n) grows no faster than f(n)</em>.
</li>
<li style="text-align: justify;">
  <strong>T(n) = Ω(f(n))</strong> <em>(Omega)</em> means that the growth rate of <strong>T(n)</strong> is asymptotically <strong>greater than or equal to</strong> to the growth rate of <strong>f(n)</strong>. <em>Lingo: T(n) grows no faster than f(n).</em>
</li>
<li style="text-align: justify;">
  <strong>T(n) = Θ(f(n))</strong> <em>(Theta)</em> means that the growth rate of <strong>T(n)</strong> is asymptotically <strong>equal to</strong> to the growth rate of <strong>f(n)</strong>.
</li>

**Hierarchy of Big-oh**

Functions ranked in increasing order of growth:

  * 1
  * log log n
  * log n
  * n
  * n log n
  * n<sup>2</sup>
  * n<sup>2</sup> log n
  * n<sup>3</sup>
  * &#8230;
  * 2<sup>n</sup>
  * n!
  * n<sup>n</sup>

Program loop evaluation:

<p style="text-align: justify;">
  <strong>O(n)</strong> &#8211; Adding to the loop counter means that the loop runtime grows linearly when compared to it&#8217;s maximum value n.
</p>

<pre class="lang:c++ decode:true">for (int i = 0; i &lt; n; i += c) // O(n)
    statement(s);</pre>

<p style="text-align: justify;">
  <strong>O(log n) &#8211;</strong> Multiplying the loop counter means that the maximum value n must grow exponentially to linearly increase the loop runtime &#8211; therefore it is logarithmic. The loop executes it&#8217;s body exactly <strong>log<sub>c</sub>n</strong> times.
</p>

<pre class="lang:c++ decode:true">for (int i = 0; i &lt; n; i *= c) // O(log n)
    statement(s);</pre>

<p style="text-align: justify;">
  <strong>O(n<sup>2</sup>) &#8211;</strong> The loop maximum is n<sup>2</sup> so the runtime is quadratic. Loop executes it&#8217;s body exactly (n<sup>2</sup> / c) times.
</p>

<pre class="lang:c++ decode:true">for (int i = 0; i &lt; n * n; i += c)
    statement(s);</pre>

<p style="text-align: justify;">
  <strong>O(n<sup>2</sup>)</strong> &#8211; Nesting loops multiplies their runtimes.
</p>

<pre class="lang:c++ decode:true">for (int i = 0; i &lt; n; i += c){
   for (int j = 0; j &lt; n; i += c){
       statement;
   }
}
</pre>

<p style="text-align: justify;">
  <strong>O(n) / O (n log n) </strong>&#8211; loops in sequence add together their runtimes, which means the loop set with the larger runtime dominates:
</p>

<pre class="lang:c decode:true ">for (int i = 0; i &lt; n; i += c) {         // O(n)
   statement;
}

for (int i = 0; i &lt; n; i += c) {         // O(n log n)
    for (int j = 0; j &lt; n; i *= c){
        statement;
    }
}</pre>

Additional details:

  * <a href="https://en.wikipedia.org/wiki/Big_O_notation" target="_blank">https://en.wikipedia.org/wiki/Big_O_notation</a>

&nbsp;