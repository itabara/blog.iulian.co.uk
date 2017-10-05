---
title: Pretty Format Bytes into KB, MB or GB
author: Iulian
type: post
date: 2012-07-12T12:20:23+00:00
url: /2012/07/pretty-format-bytes-into-kb-mb-or-gb/
categories:
  - Source code

---
<pre class="lang:c# decode:true " >/// &lt;summary&gt;
/// http://sharpertutorials.com/pretty-format-bytes-kb-mb-gb/
/// &lt;/summary&gt;
/// &lt;param name="bytes"&gt;&lt;/param&gt;
/// &lt;returns&gt;&lt;/returns&gt;
public string FormatBytes(long bytes)
{
  const int scale = 1024;
  string[] orders = new string[] { "GB", "MB", "KB", "Bytes" };
  long max = (long)Math.Pow(scale, orders.Length - 1);
  
  foreach (string order in orders)
  {
    if ( bytes &gt; max )
      return string.Format("{0:##.##} {1}", decimal.Divide( bytes, max ), order);
  
    max /= scale;
  }
  return "0 Bytes";
}</pre>