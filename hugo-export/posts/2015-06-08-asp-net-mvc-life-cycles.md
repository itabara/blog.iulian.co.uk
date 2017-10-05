---
title: Asp.net MVC Life Cycles
author: Iulian
type: post
date: 2015-06-08T19:09:05+00:00
url: /2015/06/asp-net-mvc-life-cycles/
categories:
  - Asp.net MVC

---
<p style="text-align: justify;">
  At a high level, life cycle is a sequence of steps or events used to handle some type of request or change of an application state.
</p>

<p style="text-align: justify;">
  <strong>1. Application life cycle</strong> &#8211; refers to the time when the application process starts to run in IIS (Application start) until the process stops (Application end).
</p>

<p style="text-align: justify;">
  <strong>2. MVC Request life cycle</strong> &#8211; the sequence of events that happens every time an HTTP request is handled by the application.
</p>

### MVC Request life cycle

<div id="attachment_600" style="width: 1091px" class="wp-caption aligncenter">
  <a href="http://www.iuliantabara.com/wp-content/uploads/2015/06/Asp.net-MVC-Request-Life-Cycle.jpg"><img class="wp-image-600 size-full" src="http://www.iuliantabara.com/wp-content/uploads/2015/06/Asp.net-MVC-Request-Life-Cycle.jpg" alt="Asp.net MVC Request Life Cycle" width="1081" height="450" srcset="https://www.iuliantabara.com/wp-content/uploads/2015/06/Asp.net-MVC-Request-Life-Cycle.jpg 1081w, https://www.iuliantabara.com/wp-content/uploads/2015/06/Asp.net-MVC-Request-Life-Cycle-300x125.jpg 300w, https://www.iuliantabara.com/wp-content/uploads/2015/06/Asp.net-MVC-Request-Life-Cycle-1024x426.jpg 1024w" sizes="(max-width: 1081px) 100vw, 1081px" /></a>
  
  <p class="wp-caption-text">
    Asp.net MVC Request Life Cycle
  </p>
</div>

<p style="text-align: justify;">
  <strong>Routing:</strong> The entry point for every MVC Application begins with Routing. After the application receives the request, it uses URL Routing Module to handle the request. The routing module is responsible for matching the incoming urls with the routes defined in the application. All routes have an associated MVC Route Handler. If the request is matched with one of the routes defined into application, the MVC Route Handler executes and retrieves an instance of MVC HttpHandler.
</p>

<p style="text-align: justify;">
  <strong>Controller initialization:</strong> The MVC HttpHandler begins the process of initializing and executing a controller. MVC framework converts our routing data into a specific controller that handle the request. This is accomplished by Controller Factory and Activator. This is the step where dependency injection are resolved. The next major step after controller creation is action execution.
</p>

<p style="text-align: justify;">
  <strong>Action execution:</strong> A component called Action Invoker finds and selects an appropriate Action Method to invoke in our controller. Before the method is called, Model Binding takes place and it maps the data from HttpRequest to parameters into our Action Methods. Action Filters are called before and after the method creates an Action Result.
</p>

<p style="text-align: justify;">
  <strong>Result execution</strong>: MVC separates declaring of the result from executing the result. If the result is a View type, the View Engine is called and it&#8217;s responsible for finding and rendering our view. If the result it&#8217;s not a view, the ActionResult executes on it&#8217;s own.
</p>

#### HttpModules and HttpHandlers

From the developers point of view the difference between those are: one implements `IHttpModule` interface another implements `IHttpHandler` interface.

**Module** participates in the request processing of every request in order to change or add to it in some way.

**Handler** is responsible for handling the request and producing the response for specific content types.