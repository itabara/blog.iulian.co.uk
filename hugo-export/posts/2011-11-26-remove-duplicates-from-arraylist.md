---
title: Remove Duplicates From ArrayList
author: Iulian
type: post
date: 2011-11-26T12:10:36+00:00
url: /2011/11/remove-duplicates-from-arraylist/
categories:
  - Source code

---
Usual way: 

<pre class="lang:c# decode:true " >private static ArrayList RemoveDuplicates(ArrayList arrList)
{
        ArrayList list = new ArrayList();
        foreach (string item in arrList)
        {
             if (!list.Contains(item))
            {
                list.Add(item);
            }
        }
        return list;
}</pre>

Nice way: 

<pre class="lang:c# decode:true " >private static string[] RemoveDuplicates(ArrayList arrList)
{
        HashSet Hset = new HashSet((string[])arrList.ToArray(typeof(string)));
        string[] Result = new string[Hset.Count];
        Hset.CopyTo(Result);
        return Result;
}</pre>

Nice and clean 🙂