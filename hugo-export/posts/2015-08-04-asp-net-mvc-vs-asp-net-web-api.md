---
title: 'Asp.net MVC vs  Asp.net Web API'
author: Iulian
type: post
date: 2015-08-04T11:15:11+00:00
url: /2015/08/asp-net-mvc-vs-asp-net-web-api/
categories:
  - Asp.net MVC
  - Web API

---
<p style="text-align: justify;">
  Asp.net MVC framework can return JSON data by using JsonResult and can also handle simple AJAX requests. Asp.net Web API is the new framework for building HTTP services with easy and simple way.
</p>

<ol class="orderlist">
  <li>
    <p style="text-align: justify;">
      Asp.Net MVC is used to create web applications that returns both views and data but Asp.Net Web API is used to create full blown HTTP services with easy and simple way that returns only data not view.
    </p>
  </li>
  
  <li>
    <p style="text-align: justify;">
      Web API helps to build REST-ful services over the .NET Framework and it also support content-negotiation(it&#8217;s about deciding the best response format data that could be acceptable by the client. it could be JSON, XML, ATOM or other formatted data), self hosting which are not in MVC.
    </p>
  </li>
  
  <li>
    <p style="text-align: justify;">
      Web API also takes care of returning data in particular format like JSON, XML or any other based upon the Accept header in the request and you don&#8217;t worry about that. MVC only return data in JSON format using JsonResult.
    </p>
  </li>
  
  <li>
    <p style="text-align: justify;">
      In Web API the request are mapped to the actions based on HTTP verbs but in MVC it is mapped to actions name.
    </p>
  </li>
  
  <li>
    <p style="text-align: justify;">
      Asp.Net Web API is new framework and part of the core ASP.NET framework. The model binding, filters, routing and others MVC features exist in Web API are different from MVC and exists in the new <code>&lt;a href="https://msdn.microsoft.com/en-us/library/system.web.http%28v=vs.118%29.aspx" target="_blank">System.Web.Http&lt;/a></code> assembly. In MVC, these featues exist with in <code>&lt;a href="https://msdn.microsoft.com/en-us/library/system.web.mvc%28v=vs.118%29.aspx" target="_blank">System.Web.Mvc&lt;/a></code>. Hence Web API can also be used with Asp.Net and as a stand alone service layer.
    </p>
  </li>
  
  <li>
    <p style="text-align: justify;">
      You can mix Web API and MVC controller in a single project to handle advanced AJAX requests which may return data in JSON, XML or any others format and building a full blown HTTP service. Typically, this will be called Web API self hosting.
    </p>
  </li>
  
  <li>
    <p style="text-align: justify;">
      When you have mixed MVC and Web API controller and you want to implement the authorization then you have to create two filters one for MVC and another for Web API since boths are different.
    </p>
  </li>
  
  <li>
    <p style="text-align: justify;">
      Web API is light weight architecture and except the web application it can also be used with smartphone apps.
    </p>
  </li>
</ol>