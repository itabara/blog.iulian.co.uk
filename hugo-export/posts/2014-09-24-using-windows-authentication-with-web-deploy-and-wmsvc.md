---
title: Using Windows Authentication with Web Deploy and WMSVC
author: Iulian
type: post
date: 2014-09-24T08:03:51+00:00
url: /2014/09/using-windows-authentication-with-web-deploy-and-wmsvc/
categories:
  - Web development

---
By default in Windows Server 2008 when you are using the Web Management Service (WMSVC) and Web Deploy (also known as MSDeploy) it will use Basic authentication to perform your deployments. If you want to enable Windows Authentication you will need to set a registry key so that the Web Management Service also supports using NTLM. To do this, update the registry on the server by adding a DWORD key named &#8220;**WindowsAuthenticationEnabled**&#8221; under HKEY\_LOCAL\_MACHINE\Software\Microsoft\WebManagement\Server, and set it to 1. If the Web Management Service is already started, the setting will take effect after the service is restarted.

net stop wmsvc
  
net start wmsvc

To install Web Deploy please use http://technet.microsoft.com/en-us/library/dd569059.aspx