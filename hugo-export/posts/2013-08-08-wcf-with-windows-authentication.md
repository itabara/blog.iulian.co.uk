---
title: WCF with Windows authentication
author: Iulian
type: post
date: 2013-08-08T19:41:28+00:00
url: /2013/08/wcf-with-windows-authentication/
categories:
  - .Net Framework
tags:
  - WCF

---
I ran into this problem because I wanted a WCF service hosted in IIS 7.5/8 on a website that had Windows Authentication enabled.
  
The service configuration looks much simpler in the config file of the web application hosting the WCF service:

Server configuration:

<pre class="lang:c# decode:true " >&lt;system.serviceModel&gt;
    &lt;bindings&gt;
      &lt;basicHttpBinding&gt;
        &lt;binding name="BasicHttpEndpointBinding"&gt;
          &lt;security mode="TransportCredentialOnly"&gt;
            &lt;transport clientCredentialType="Windows" /&gt;
          &lt;/security&gt;
        &lt;/binding&gt;
      &lt;/basicHttpBinding&gt;
    &lt;/bindings&gt;
    &lt;services&gt;
      &lt;service behaviorConfiguration="WCFTest.Service1Behavior" name="WCFTest.Service1"&gt;
        &lt;endpoint address="" binding="basicHttpBinding"
          bindingConfiguration="BasicHttpEndpointBinding"
          name="BasicHttpEndpoint" contract="WCFTest.IService1"&gt;
        &lt;/endpoint&gt;
      &lt;/service&gt;
    &lt;/services&gt;
    &lt;behaviors&gt;
      &lt;serviceBehaviors&gt;
        &lt;behavior name="WCFTest.Service1Behavior"&gt;
          &lt;!-- To avoid disclosing metadata information, set the value below to false and remove the metadata endpoint above before deployment --&gt;
          &lt;serviceMetadata httpGetEnabled="true"/&gt;
          &lt;!-- To receive exception details in faults for debugging purposes, set the value below to true.  Set to false before deployment to avoid disclosing exception information --&gt;
          &lt;serviceDebug includeExceptionDetailInFaults="false"/&gt;
        &lt;/behavior&gt;
      &lt;/serviceBehaviors&gt;
    &lt;/behaviors&gt;
  &lt;/system.serviceModel&gt;</pre>

Remember to set this to the WCF service:

<pre class="lang:c# decode:true " >[AspNetCompatibilityRequirements(RequirementsMode = AspNetCompatibilityRequirementsMode.Required)]</pre>

Client configuration:

<pre class="lang:c# decode:true " >&lt;system.serviceModel&gt;
    &lt;bindings&gt;
      &lt;basicHttpBinding&gt;
        &lt;binding name="BasicHttpEndpoint" closeTimeout="00:01:00" openTimeout="00:01:00"
            receiveTimeout="00:10:00" sendTimeout="00:01:00" allowCookies="false"
            bypassProxyOnLocal="false" hostNameComparisonMode="StrongWildcard"
            maxBufferSize="65536" maxBufferPoolSize="524288" maxReceivedMessageSize="65536"
            messageEncoding="Text" textEncoding="utf-8" transferMode="Buffered"
            useDefaultWebProxy="true"&gt;
          &lt;readerQuotas maxDepth="32" maxStringContentLength="8192" maxArrayLength="16384"
              maxBytesPerRead="4096" maxNameTableCharCount="16384" /&gt;
          &lt;security mode="TransportCredentialOnly"&gt;
            &lt;transport clientCredentialType="Windows" proxyCredentialType="None"
                realm="" /&gt;
            &lt;message clientCredentialType="UserName" algorithmSuite="Default" /&gt;
          &lt;/security&gt;
        &lt;/binding&gt;
      &lt;/basicHttpBinding&gt;
    &lt;/bindings&gt;
    &lt;client&gt;
      &lt;endpoint address="http://machinename/WCFTest/Service1.svc"
          binding="basicHttpBinding" bindingConfiguration="BasicHttpEndpoint"
          contract="IService1" name="BasicHttpEndpoint" /&gt;
    &lt;/client&gt;
  &lt;/system.serviceModel&gt;</pre>

I ended up using basicHttpBinding as explained in the <a href="http://msdn.microsoft.com/en-us/library/ff648505.aspx" title="article" target="_blank">article </a>. Client config is generated using &#8220;svcutil&#8221;.