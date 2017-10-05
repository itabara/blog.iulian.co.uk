---
title: jQuery UI and autocomplete TextBox
author: Iulian
type: post
date: 2011-12-06T12:05:02+00:00
url: /2011/12/jquery-ui-and-autocomplete-textbox/
categories:
  - Asp.net
tags:
  - jquery

---
This article demonstrates how to add autocomplete feature to asp.net TextBox, exposing autocomplete values via WebService.

> The Autocomplete widget is one of the widgets provided in jQuery UI and provides suggestions while you type into the field. jQuery UI is a free widget and interaction library built on top of the jQuery JavaScript Library, that you can use to build highly interactive web applications.

First let’s create the webservice that will generate some random values. 

<pre class="lang:c# decode:true " >using System;
using System.Collections.Generic;
using System.Linq;
using System.Web.Services;
 
/// &lt;summary&gt;
/// Summary description for ArticlesService
/// &lt;/summary&gt;
[WebService(Namespace = "http://tempuri.org/")]
[WebServiceBinding(ConformsTo = WsiProfiles.BasicProfile1_1)]
// To allow this Web Service to be called from script, using ASP.NET AJAX, uncomment the following line.
[System.Web.Script.Services.ScriptService]
public class ArticlesService : System.Web.Services.WebService {
 
    public ArticlesService () {
 
        //Uncomment the following line if using designed components
        //InitializeComponent();
    }
 
    [WebMethod]
    public List&lt;string&gt; FetchArticles(string keyword)
    {
        Random random = new Random();
        var prefix = Enumerable.Range(1, 100).Select(i =&gt; random.Next()).Select(k =&gt; (keyword + "-" + k).ToString());
        return prefix.ToList();
    }
}</pre>

As you see in comments, please make sure [System.Web.Script.Services.ScriptService] is not commented otherwise you’ll have issues.

Below the code that do our job:

<pre class="lang:c# decode:true " >&lt;%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %&gt;
 
&lt;!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"&gt;
&lt;html xmlns="http://www.w3.org/1999/xhtml"&gt;
&lt;head runat="server"&gt;
    &lt;title&gt;jQuery Autocomplete&lt;/title&gt;
    &lt;link href="http://ajax.googleapis.com/ajax/libs/jqueryui/1.8.1/themes/base/jquery-ui.css"
        rel="stylesheet" type="text/css" /&gt;
    &lt;script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.4.2/jquery.min.js"&gt;&lt;/script&gt;
    &lt;script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jqueryui/1.8.1/jquery-ui.min.js"&gt;&lt;/script&gt;
    &lt;script type="text/javascript"&gt;
        $(function () {
            $(".tb").autocomplete({
                source: function (request, response) {
                    $.ajax({
                        url: "ArticlesService.asmx/FetchArticles",
                        data: "{ 'keyword': '" + request.term + "' }",
                        dataType: "json",
                        type: "POST",
                        contentType: "application/json; charset=utf-8",
                        dataFilter: function (data) { return data; },
                        success: function (data) {
                            response($.map(data.d, function (item) {
                                return {
                                    value: item
                                    // use item.Property if you bind to complex object (entity)
                                }
                            }))
                        },
                        error: function (XMLHttpRequest, textStatus, errorThrown) {
                            alert(textStatus);
                        }
                    });
                },
                minLength: 2
            });
        });
    &lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;form id="form1" runat="server"&gt;
    &lt;div class="demo"&gt;
        &lt;asp:Label runat="server" ID="lblKeyword" Text="Search: "&gt;
        &lt;/asp:Label&gt;
        &lt;asp:TextBox runat="server" ID="txtSearch" CssClass="tb"&gt;
        &lt;/asp:TextBox&gt;
    &lt;/div&gt;
    &lt;/form&gt;
&lt;/body&gt;
&lt;/html&gt;</pre>