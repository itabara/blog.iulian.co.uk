---
title: 'Katana & Owin – Part II'
author: Iulian
type: post
date: 2014-01-04T20:28:48+00:00
url: /2014/01/katana-owin-ii/
categories:
  - .Net Framework
tags:
  - Katana
  - Owin
  - Web API

---
This tutorial shows how to host ASP.NET Web API in a console application, using OWIN to self-host the Web API framework.

<pre class="lang:ps decode:true " >Install-Package -IncludePrerelease Microsoft.AspNet.WebApi.OwinSelfHost</pre>

The source to add sample Web API configuration:

<pre class="lang:c# decode:true " >public class Startup
{
    public void Configuration(IAppBuilder app)
    {
        app.Use(async (environment, next) =&gt;
        {
            Console.WriteLine("Requesting path: {0}", environment.Request.Path);

            // Request section
            await next();
            // Response section
            Console.WriteLine("Response: ", environment.Response.StatusCode);
        });

        this.ConfigureWebApi(app);

        app.UseHelloWorldComponent();
    }

    private void ConfigureWebApi(IAppBuilder app)
    {
        var config = new HttpConfiguration();
        config.Routes.MapHttpRoute("DefaultApi",
            "api/{controller}/{id}",
            new { id = RouteParameter.Optional });

        app.UseWebApi(config);
    }
}</pre>

**Greeting.cs**

<pre class="lang:c# decode:true " >public class Greeting
{
    public string Text { get; set; }
}</pre>

**GreetingContoller.cs**

<pre class="lang:c# decode:true " >public class GreetingController : ApiController
{
    public Greeting Get()
    {
        return new Greeting() { Text = "Hello World !" };
    }
}</pre>