---
title: MD5 encryption – with Linq
author: Iulian
type: post
date: 2013-07-02T12:13:56+00:00
url: /2013/07/md5-encryption-with-linq/
categories:
  - Source code
  - Uncategorized

---
Here is a nice piece of code to do MD5 encryption. Click <a href="http://www.sellsbrothers.com/Posts/Details/12686" title="here" target="_blank">here</a> for the original source code.

<pre class="lang:c# decode:true " >static string GetMD5String(string s) {
    return (new MD5CryptoServiceProvider()).
             ComputeHash(Encoding.Unicode.GetBytes(s)).
             Aggregate(new StringBuilder(), (working, b) =&gt; working.
                  AppendFormat("{0:x2}", b)).ToString();
}</pre>

<pre class="lang:c# decode:true " >static string GetMD5String(string s) {
   return string.Join("", ComputeHash(Encoding.Unicode.GetBytes(s))
                                 .Select(b =&gt; b.ToString("x2")));
}</pre>