---
title: WCF vs Web API vs WCF REST vs Web Service (SOAP)
author: Iulian
type: post
date: 2015-08-04T11:21:00+00:00
url: /2015/08/wcf-vs-web-api-vs-wcf-rest-vs-web-service-soap/
categories:
  - .Net Framework
  - Asp.net MVC
  - Web API

---
<p style="text-align: justify;">
  The .Net framework has a number of technologies that allow you to create HTTP services such as Web Service, WCF and Web API.
</p>

## Web Service

<ul class="orderlist" style="list-style-type: disc;">
  <li>
    <p style="text-align: justify;">
      It is based on SOAP and return data in XML form.
    </p>
  </li>
  
  <li style="text-align: justify;">
    It support only HTTP protocol.
  </li>
  <li style="text-align: justify;">
    It is not open source but can be consumed by any client that understands xml.
  </li>
  <li>
    <p style="text-align: justify;">
      It can be hosted only on IIS.
    </p>
  </li>
</ul>

## WCF

<ul class="orderlist" style="list-style-type: disc;">
  <li>
    <p style="text-align: justify;">
      It is also based on SOAP and return data in XML form.
    </p>
  </li>
  
  <li style="text-align: justify;">
    It is the evolution of the web service(ASMX) and support various protocols like TCP, HTTP, HTTPS, Named Pipes, MSMQ.
  </li>
  <li style="text-align: justify;">
    The main issue with WCF is its tedious and extensive configuration (<a href="http://www.wcftutorial.net/" target="_blank">see WCF Tutorial</a>).
  </li>
  <li style="text-align: justify;">
    It is not open source but can be consumed by any client that understands xml.
  </li>
  <li>
    <p style="text-align: justify;">
      It can be hosted with in the application or on IIS or using windows services.
    </p>
  </li>
</ul>

## WCF Rest

<ul class="orderlist" style="list-style-type: disc;">
  <li>
    <p style="text-align: justify;">
      To use WCF as WCF <a class="link" href="http://kellabyte.com/2011/09/04/clarifying-rest/" target="_blank">Rest service</a> you have to enable webHttpBindings.
    </p>
  </li>
  
  <li style="text-align: justify;">
    It support HTTP GET and POST verbs by [WebGet] and [WebInvoke] attributes respectively.
  </li>
  <li style="text-align: justify;">
    To enable other HTTP verbs you have to do some configuration in IIS to accept request of that particular verb on .svc files
  </li>
  <li style="text-align: justify;">
    Passing data through parameters using a WebGet needs configuration. The UriTemplate must be specified
  </li>
  <li>
    <p style="text-align: justify;">
      It support XML, JSON and ATOM data format.
    </p>
  </li>
</ul>

## Web API

<ul class="orderlist" style="list-style-type: disc;">
  <li>
    <p style="text-align: justify;">
      This is the new framework for building HTTP services with easy and simple way.
    </p>
  </li>
  
  <li style="text-align: justify;">
    Web API is open source an ideal platform for building REST-ful services over the .NET Framework.
  </li>
  <li style="text-align: justify;">
    Unlike WCF Rest service, it use the full featues of HTTP (like URIs, request/response headers, caching, versioning, various content formats)
  </li>
  <li style="text-align: justify;">
    It also supports the MVC features such as routing, controllers, action results, filter, model binders, IOC container or dependency injection, unit testing that makes it more simple and robust.
  </li>
  <li style="text-align: justify;">
    It can be hosted with in the application or on IIS.
  </li>
  <li style="text-align: justify;">
    It is light weight architecture and good for devices which have limited bandwidth like smart phones.
  </li>
  <li>
    <p style="text-align: justify;">
      Responses are formatted by Web API’s MediaTypeFormatter into JSON, XML or whatever format you want to add as a MediaTypeFormatter.
    </p>
  </li>
</ul>

## To whom choose between WCF or WEB API

<ul class="orderlist" style="list-style-type: disc;">
  <li>
    <p style="text-align: justify;">
      Choose WCF when you want to create a service that should support special scenarios such as one way messaging, message queues, duplex communication etc.
    </p>
  </li>
  
  <li style="text-align: justify;">
    Choose WCF when you want to create a service that can use fast transport channels when available, such as TCP, Named Pipes, or maybe even UDP (in WCF 4.5), and you also want to support HTTP when all other transport channels are unavailable.
  </li>
  <li style="text-align: justify;">
    Choose Web API when you want to create a resource-oriented services over HTTP that can use the full features of HTTP (like URIs, request/response headers, caching, versioning, various content formats).
  </li>
  <li>
    <p style="text-align: justify;">
      Choose Web API when you want to expose your service to a broad range of clients including browsers, mobiles, iphone and tablets.
    </p>
  </li>
</ul>