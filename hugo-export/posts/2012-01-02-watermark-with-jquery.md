---
title: Watermark with jQuery
author: Iulian
type: post
date: 2012-01-02T12:02:55+00:00
url: /2012/01/watermark-with-jquery/
categories:
  - jQuery
tags:
  - jquery

---
Sometimes the design or page size limitations force you to add watermark descriptive text to textboxes in order to save space.
  
Below is a **jQuery **implementation of watermark on Asp.net **TextBox** control. **Tooltip **is rendered as title so we’ll use that value to compare with textbox value.

<pre class="lang:c# decode:true " >&lt;html xmlns="http://www.w3.org/1999/xhtml"&gt;
&lt;head runat="server"&gt;
    &lt;title&gt;Watermark jQuery&lt;/title&gt;
 
    &lt;style type="text/css"&gt;
        .water
        {
            font-family: Tahoma, Arial, sans-serif;
            font-size:75%;
            color:gray;
        }
    &lt;/style&gt;
 
    &lt;script src="Scripts/jquery-1.7.1.js" type="text/javascript"&gt;&lt;/script&gt;
    &lt;script type="text/javascript"&gt;
        $(document).ready(function () {
            $(".water").each(function () {
                $textBox = $(this);
                if ($textBox.val() != this.title) {
                    // tooltip is rendered as title
                    $textBox.removeClass("water");
                }
            });
 
            $(".water").focus(function () {
                $textBox = $(this);
                if ($textBox.val() == this.title) {
                    // tooltip is rendered as title
                    $textBox.val("");
                    $textBox.removeClass("water");
                }
            });
 
            $(".water").blur(function () {
                $textBox = $(this);
                if ($.trim($textBox.val()) == "") {
                    $textBox.val(this.title);
                    $textBox.addClass("water");
                }
            });
        })
    &lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;form id="form1" runat="server"&gt;
    &lt;div class="smallDiv"&gt;
        &lt;asp:TextBox runat="server" ID="txtFirstName" Text="Type your first name" ToolTip="Type your first name" CssClass="water"&gt;&lt;/asp:TextBox&gt;
        &lt;br /&gt;
        &lt;asp:TextBox runat="server" ID="txtLastName" Text="Type your last name" ToolTip="Type your last name" CssClass="water"&gt;&lt;/asp:TextBox&gt;
        &lt;br /&gt;
        &lt;asp:Button runat="server" ID="btnSave" Text="Save" /&gt;   
    &lt;/div&gt;
    &lt;/form&gt;
&lt;/body&gt;
&lt;/html&gt;</pre>