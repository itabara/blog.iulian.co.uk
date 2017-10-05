---
title: QueryString helper
author: Iulian
type: post
date: 2010-05-10T23:54:52+00:00
url: /2010/05/querystring-helper/
categories:
  - Source code

---
<p align="justify">
  Query strings are something we deal with constantly. I’ve come up with a helper that uses .NET Generics to handle a lot of common cases that we usually write unnecessary code for.
</p>

<pre class="lang:c# decode:true ">public class QS
{
    public static T Value&lt;t&gt;(string parameterName) where T : IConvertible
    {
        if (HttpContext.Current == null)
            throw new
                InvalidOperationException("Cannot call QS&lt;t&gt; when HttpContext.Current is null");
        if (string.IsNullOrEmpty(HttpContext.Current.Request[parameterName]))
            throw new
                InvalidOperationException(
                    string.Format("Expected query string parameter named '{0}' to exist", parameterName));
        return Value&lt;t&gt;(parameterName, default(T));
    }
  
    public static T Value&lt;t&gt;(string parameterName, T defaultValue) where T : IConvertible
    {
        if (HttpContext.Current == null)
            throw new
                InvalidOperationException("Cannot call QS&lt;t&gt; when HttpContext.Current is null");
        HttpRequest request = HttpContext.Current.Request;
        string input;
        if (request.QueryString[parameterName] != null
            && !string.IsNullOrEmpty(request.QueryString[parameterName]))
               input = request.QueryString[parameterName];
        else
            return defaultValue;
        T value;
        try
        {
            value = (T)Convert.ChangeType(input, typeof(T));
        }
        catch (Exception ex)
        {
            throw new
                InvalidOperationException(
                    string.Format("Unable to convert query string parameter named '{0}' with value of '{1}' to type {2}",
                        parameterName, input, typeof(T).FullName), ex);
        }
        return value;
    }
}</pre>

Usage:

<pre class="lang:c# decode:true ">// Required. Will throw exceptions if the query string value is null or empty string
int personID = QS.Value("personID");
string keywords = QS.Value("keywords");
// Optional. Use default value if query string value is null or empty
int startIndex = QS.Value("startIndex", 0);</pre>