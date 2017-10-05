---
title: 'Katana & Owin – Part I'
author: Iulian
type: post
date: 2014-01-01T18:34:51+00:00
url: /2014/01/katana-owin/
categories:
  - Web development
tags:
  - Katana
  - Owin

---
<p align="justify">
  <a title="Katana" href="http://katanaproject.codeplex.com/" target="_blank">Katana</a> is a flexible set of components for building and hosting OWIN-based web applications.<br /><a title="OWIN" href="http://www.owin.org/" target="_blank">OWIN</a> defines a standard interface between .NET web servers and web applications. The goal of the OWIN interface is to decouple server and application, encourage the development of simple modules for .NET web development, and, by being an open standard, stimulate the open source ecosystem of .NET web development tools.
</p>

Let&#8217;s try to create the &#8216;clasic&#8217; Hello World! application for **Katana**.  
Start **Visual Studio 2012** then create a new **Console Application**.  
**Ctrl + Q** -> Package Manager Console

<pre class="lang:ps decode:true ">install-package -IncludePrerelease Microsoft.Owin.Hosting</pre>

then

<pre class="lang:ps decode:true ">install-package -IncludePrerelease Microsoft.Owin.Host.HttpListener</pre>

for diagnostics

<pre class="lang:ps decode:true ">install-package -IncludePrerelease Microsoft.Owin.Diagnostics</pre>

The source code is:

<pre class="lang:c# decode:true ">using System;
using Microsoft.Owin.Hosting;
using Owin;

namespace KatanaDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            string uri = "http://localhost:8080";
            using (WebApp.Start&lt;Startup&gt;(uri))
            {
                Console.WriteLine("Started !");
                Console.ReadKey();
                Console.WriteLine("Stopping !");
            }
        }
    }

    public class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            app.Run(ctx =&gt;
            {
                return ctx.Response.WriteAsync("Hello world !");
            });
        }
    }
}
</pre>

<p align="justify">
  If you want to have a nice welcome screen instead of &#8220;Hello world !&#8221; just add below line of code and ignore the rest of the code from Configuration method.
</p>

<pre class="lang:c# decode:true ">app.UseWelcomePage();</pre>

#### Under the hood

Owin describes one main component which is the following interface:

<pre class="lang:c# decode:true ">Func&lt;IDictionary&lt;string, object&gt;,Task&gt;</pre>

<p align="justify">
  This is a function that accepts a simple dictionary of objects, keyed by a string identifier. The function itself returns a task. The object in the dictionary in this instance will vary depending on what the key is referring to.
</p>

More often, you will see it referenced like this:

<pre class="lang:c# decode:true ">using AppFunc = Func&lt;IDictionary&lt;string, object&gt;,Task&gt;</pre>

An actual implementation might look like this:

<pre class="lang:c# decode:true ">public Task Invoke(IDictionary&lt;string, object&gt; environment) 
{ 

   var someObject = environment[“some.key”] as SomeObject; 
   // etc… 
}
</pre>

<p align="justify">
  This is essentially how the “environment” or information about the HTTP context is passed around. Looking at the environment argument of this method, you could interrogate it as follows:
</p>

<pre class="lang:c# decode:true ">var httpRequestPath = environment[“owin.RequestPath”] as string; 
Console.WriteLine(“Your Path is: [{0}]”,httpRequestPath);
</pre>

<p align="justify">
  if a HttpRequest was being made to ‘http://localhost:8080/Content/Main.css” then the output would be:
</p>

Your Path is [/Content/Main.css]

In addition, while not part of the spec, the **IAppBuilder** interface is also core to the functioning of an **Owin** module (or ‘middleware’ in Owin speak):

<pre class="lang:c# decode:true ">public interface IAppBuilder
{
    IDictionary&lt;string, object&gt; Properties { get; }

    object Build(Type returnType);
    IAppBuilder New();
    IAppBuilder Use(object middleware, params object[] args);
}</pre>

<p align="justify">
  The <strong>IAppBuilder</strong> interface acts as the ‘glue’ or host to bring any registered <strong>Owin</strong> compatible libraries/modules together. With support of this simple mechanism, you can now write isolated components that deal with specific parts of functionality related to Http requests. You can then chain them together to build capabilities of my Http server. You can literally chain together <strong>Owin</strong> components to form a pipeline of only the necessary features you want. <br />Basically you can build or use a custom host, then you can insert whatever custom modules into the Http request processing pipeline. <strong>Owin</strong> provides the specification for writing those modules that make it easy to insert into the chain.
</p>

#### Sample Katana component

Owin components are also known as _middleware_.

<pre class="lang:c# decode:true ">using System;
using System.IO;
using System.Collections.Generic;
using System.Threading.Tasks;

using Owin;
using Microsoft.Owin.Hosting;

namespace KatanaDemo
{
    using AppFunc = Func&lt;IDictionary&lt;string, object&gt;, Task&gt;;
    
    class Program
    {
        static void Main(string[] args)
        {
            string uri = "http://localhost:8080";
            using (WebApp.Start&lt;Startup&gt;(uri))
            {
                Console.WriteLine("Started !");
                Console.ReadKey();
                Console.WriteLine("Stopping !");
            }
        }
    }

    public class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            // how to use your customer component
            app.Use&lt;HelloWorldComponent&gt;();            
        }
    }

    public class HelloWorldComponent
    {
        AppFunc _next;
        
        public HelloWorldComponent(AppFunc next)
        {
            _next = next;               
        }

        public Task Invoke(IDictionary&lt;string, object&gt; environment)
        {
            var response = environment["owin.ResponseBody"] as Stream;
            using (StreamWriter writter = new StreamWriter(response))
            {
                return writter.WriteAsync("Hello !!");
            }
        }
    }
}
</pre>

<p align="justify">
  Wrap component in extension method and invoke it like this which resembles the Welcome page invocation.
</p>

<pre class="lang:c# decode:true ">public static class AppBuilderExtensions
{
        public static void UseHelloWorldComponent(this IAppBuilder app)
        {
            app.Use&lt;HelloWorldComponent&gt;();
        }
}
</pre>

<pre class="lang:c# decode:true ">public class Startup
{
        public void Configuration(IAppBuilder app)
        {
            // how to use your customer component
            //app.Use&lt;HelloWorldComponent&gt;();
            app.UseHelloWorldComponent();
        }
}</pre>

See <a title="Owin specifications" href="http://owin.org/spec/owin-1.0.0-RC0.html" target="_blank">owin</a> for full owin specifications.

#### Chaining components

<pre class="lang:c# decode:true ">public class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            // how to use your customer component
            //app.Use&lt;HelloWorldComponent&gt;();

            app.Use(async (environment, next) =&gt;
            {
                foreach (var pair in environment.Environment)
                {
                    Console.WriteLine("{0} : {1}", pair.Key, pair.Value);
                }
                
                // Request section
                await next();
                // Response section
            });

            app.Use(async (environment, next) =&gt;
            {
                Console.WriteLine("Requesting path: {0}", environment.Request.Path);
                
                // Request section
                await next();
                // Response section
                Console.WriteLine("Response: ", environment.Response.StatusCode);
            });

            app.UseHelloWorldComponent();
        }
    }</pre>