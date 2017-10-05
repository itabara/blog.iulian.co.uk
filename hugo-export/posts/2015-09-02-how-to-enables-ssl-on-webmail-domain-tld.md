---
title: How to enables SSL on webmail.domain.tld
author: Iulian
type: post
date: 2015-09-02T17:19:12+00:00
url: /2015/09/how-to-enables-ssl-on-webmail-domain-tld/
categories:
  - DevOps
  - Linux
tags:
  - roundcube

---
In order to secure your roundcube webmail with your SSL certificate you need to follow below steps in your Plesk CP.

  1. Go to Server -> SSL Certificates -> Add your SSL certificate here if you haven&#8217;t already.
  2. Go to Server -> IP Addresses -> [your public IP] -> Change the SSL certificate to the certificate you added in Step 1
  3. Add <pre class="lang:sh decode:true">RewriteCond %{HTTPS} off
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}</pre>
    
    to **/etc/apache2/plesk.conf.d/roundcube.htaccess.inc** somewhere after &#8220;RewriteEngine On&#8221;</li> </ol> 
    
    Enjoy your <a href="https://webmail.domain.tld" target="_blank">https://webmail.domain.tld</a>.