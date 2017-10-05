---
title: Pretty Date Formating
author: Iulian
type: post
date: 2011-12-02T12:07:51+00:00
url: /2011/12/pretty-date-formating/
categories:
  - Source code

---
You want to display pretty, nicely formatted dates in your C# program. For example, you need to show &#8220;1 hour ago&#8221; or &#8220;1 minute ago&#8221;, instead of seconds. 

Source <a href="http://www.dotnetperls.com/pretty-date " title="here" target="_blank">here</a>

<pre class="lang:c# decode:true " >using System;
class Program
{
    static void Main()
    {
    // Test 90 seconds ago.
    Console.WriteLine(GetPrettyDate(DateTime.Now.AddSeconds(-90)));
    // Test 25 minutes ago.
    Console.WriteLine(GetPrettyDate(DateTime.Now.AddMinutes(-25)));
    // Test 45 minutes ago.
    Console.WriteLine(GetPrettyDate(DateTime.Now.AddMinutes(-45)));
    // Test 4 hours ago.
    Console.WriteLine(GetPrettyDate(DateTime.Now.AddHours(-4)));
    // Test 15 days ago.
    Console.WriteLine(GetPrettyDate(DateTime.Now.AddDays(-15)));
    }
 
    static string GetPrettyDate(DateTime d)
    {
    // 1.
    // Get time span elapsed since the date.
    TimeSpan s = DateTime.Now.Subtract(d);
 
    // 2.
    // Get total number of days elapsed.
    int dayDiff = (int)s.TotalDays;
 
    // 3.
    // Get total number of seconds elapsed.
    int secDiff = (int)s.TotalSeconds;
 
    // 4.
    // Don't allow out of range values.
    if (dayDiff &lt; 0 || dayDiff &gt;= 31)
    {
        return null;
    }
 
    // 5.
    // Handle same-day times.
    if (dayDiff == 0)
    {
        // A.
        // Less than one minute ago.
        if (secDiff &lt; 60)
        {
        return "just now";
        }
        // B.
        // Less than 2 minutes ago.
        if (secDiff &lt; 120)
        {
        return "1 minute ago";
        }
        // C.
        // Less than one hour ago.
        if (secDiff &lt; 3600)
        {
        return string.Format("{0} minutes ago",
            Math.Floor((double)secDiff / 60));
        }
        // D.
        // Less than 2 hours ago.
        if (secDiff &lt; 7200)
        {
        return "1 hour ago";
        }
        // E.
        // Less than one day ago.
        if (secDiff &lt; 86400)
        {
        return string.Format("{0} hours ago",
            Math.Floor((double)secDiff / 3600));
        }
    }
    // 6.
    // Handle previous days.
    if (dayDiff == 1)
    {
        return "yesterday";
    }
    if (dayDiff &lt; 7)
    {
        return string.Format("{0} days ago",
        dayDiff);
    }
    if (dayDiff &lt; 31)
    {
        return string.Format("{0} weeks ago",
        Math.Ceiling((double)dayDiff / 7));
    }
    return null;
    }
}</pre>