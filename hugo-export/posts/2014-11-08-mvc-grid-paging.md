---
title: MVC Grid Paging with EntityFramework or Stored Procedures
author: Iulian
type: post
date: 2014-11-08T17:09:24+00:00
url: /2014/11/mvc-grid-paging/
categories:
  - Asp.net MVC

---
Here we&#8217;ll implement Custom Paging in webgrid in MVC4 application.
  
It is a very essential approach to use paging technique in applications where lot of data to be loaded from the database.

<pre class="lang:sql decode:true">CREATE TABLE [dbo].[Customer] (
    [CustomerID]   INT            IDENTITY (1, 1) NOT NULL,
    [CustomerName] NVARCHAR (100) NULL,
    [Address]      NVARCHAR (250) NULL,
    [EmailAddress] NVARCHAR (150) NULL,
    [City]         NVARCHAR (50)  NULL,
    [PostCode]     NVARCHAR (20)  NULL,
    PRIMARY KEY CLUSTERED ([CustomerID] ASC)
);
</pre>

**HomeController.cs**

<pre class="lang:c# decode:true">using System.Collections.Generic;
using System.Linq;
using System.Web.Mvc;

namespace MVCGridPaging.Controllers
{
    public class HomeController : Controller
    {
        public ActionResult Index(int page = 1)
        {
            int pageSize = 10;
            int noPages = 0;
            int noRecords = 0;

            List customerList = new List();
            using (MyDatabaseEntities db = new MyDatabaseEntities())
            {
                noRecords = db.Customers.Count();
                noPages = (noRecords / pageSize) + ((noRecords % pageSize) &gt; 0 ? 1:0);
                int offset = (page - 1) * pageSize;
                customerList = db.Customers.OrderBy(cust =&gt; cust.CustomerID).Skip(offset).Take(pageSize).ToList();
            }

            ViewBag.TotalRows = noRecords;
            ViewBag.PageSize = pageSize;

            return View(customerList);
        }
    }
}
</pre>

**Index.cshtml**

<pre class="lang:html decode:true">@model List

@{
    ViewBag.Title = "MVC Grid Paging";
    // create instance of the grid
    var grid = new WebGrid(rowsPerPage: ViewBag.PageSize, canPage: true);
    grid.Bind(source: Model, autoSortAndPage: false, rowCount: ViewBag.TotalRows);
}</pre>

## Custom paging

<pre class="lang:c# decode:true">@grid.GetHtml(tableStyle: "gridtable", mode: WebGridPagerModes.All, firstText:"First", lastText:"Last", nextText:"Next", previousText:"Previous", columns: grid.Columns( grid.Column("CustomerID", "Customer ID"), grid.Column("CustomerName", "Customer Name"), grid.Column("Address", "Address"), grid.Column("EmailAddress", "Email Address"), grid.Column("City", "City"), grid.Column("PostCode", "PostCode") ) )
</pre>

Or you can use <a title="PagedList" href="https://www.nuget.org/packages/PagedList" target="_blank">PagedList</a> to do the paging code.

Updated version using Stored Procedures on MyGit.