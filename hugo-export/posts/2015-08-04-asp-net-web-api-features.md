---
title: Asp.net Web API Features
author: Iulian
type: post
date: 2015-08-04T11:06:09+00:00
url: /2015/08/asp-net-web-api-features/
categories:
  - Web API

---
<p style="text-align: justify;">
  Web API is the great framework for exposing your data and service to different-different devices. It is open source an ideal platform for building REST-ful services over the .NET Framework. Unlike WCF Rest service, it use the full features of HTTP (like URIs, request/response headers, caching, versioning, various content formats) and you don&#8217;t need to define any extra config settings for different devices unlike WCF Rest service.
</p>

## Web API Features

<ul class="orderlist" style="list-style-type: disc;">
  <li style="text-align: justify;">
    It supports convention-based CRUD Actions since it works with HTTP verbs GET, POST, PUT and DELETE.
  </li>
  <li style="text-align: justify;">
    Responses have an Accept header and HTTP status code.
  </li>
  <li style="text-align: justify;">
    Responses are formatted by Web API’s MediaTypeFormatter into JSON, XML or whatever format you want to add as a MediaTypeFormatter.
  </li>
  <li style="text-align: justify;">
    It may accepts and generates the content which may not be object oriented like images, PDF files etc.
  </li>
  <li style="text-align: justify;">
    It has automatic support for OData. Hence by placing the new [Queryable] attribute on a controller method that returns IQueryable, clients can use the method for OData query composition.
  </li>
  <li style="text-align: justify;">
    It can be hosted with in the application or on IIS.
  </li>
  <li style="text-align: justify;">
    It also supports the MVC features such as routing, controllers, action results, filter, model binders, IOC container or dependency injection that makes it more simple and robust.
  </li>
</ul>

## Why to choose Web API ?

<ul class="orderlist" style="list-style-type: disc;">
  <li style="text-align: justify;">
    If we need a Web Service and don’t need SOAP, then ASP.Net Web API is best choice.
  </li>
  <li style="text-align: justify;">
    It is Used to build simple, non-SOAP-based HTTP Services on top of existing WCF message pipeline.
  </li>
  <li style="text-align: justify;">
    It doesn&#8217;t have tedious and extensive configuration like WCF REST service.
  </li>
  <li style="text-align: justify;">
    Simple service creation with Web API. With WCF REST Services, service creation is difficult.
  </li>
  <li style="text-align: justify;">
    It is only based on HTTP and easy to define, expose and consume in a REST-ful way.
  </li>
  <li style="text-align: justify;">
    It is light weight architecture and good for devices which have limited bandwidth like smart phones.
  </li>
  <li style="text-align: justify;">
    It is open source.
  </li>
</ul>