---
title: 'Easy RFC822 formatted dates with C#'
author: Iulian
type: post
date: 2013-07-02T11:07:51+00:00
url: /2013/07/date-conversion-to-rfc822/
categories:
  - Source code

---
<pre class="lang:c# decode:true " >namespace TestRFC822
{
    public static partial class Extensions
    {
        public static string ToRFC822String(this DateTime timestamp)
        {
            return timestamp.ToString("ddd',' d MMM yyyy HH':'mm':'ss")
                + " "
                + timestamp.ToString("zzzz").Replace(":", "");
        }
    }
}
</pre>