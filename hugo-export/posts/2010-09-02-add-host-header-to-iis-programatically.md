---
title: Add host header to IIS programatically
author: Iulian
type: post
date: 2010-09-02T00:12:12+00:00
url: /2010/09/add-host-header-to-iis-programatically/
categories:
  - Uncategorized
tags:
  - IIS

---
More details <a href="http://www.microsoft.com/technet/prodtechnol/WindowsServer2003/Library/IIS/0ed054d1-ca3a-495f-bad4-3a251e5a07a3.mspx?mfr=true" title="here" target="_blank">here</a>

<pre class="lang:c# decode:true " >using System;
namespace IISProgram
{
    public class IIS
    {
        public static void Main(string[] args)
        {
            AddHostHeader(1, "127.0.0.1", 8080, "my");
            AddHostHeader(1, null, 8081, null);
        }
 
        static void AddHostHeader(int? websiteID, string ipAddress, int? port, string hostname)
        {
            using (var directoryEntry = new DirectoryEntry("IIS://localhost/w3svc/" + websiteID.ToString()))
            {
                var bindings = directoryEntry.Properties["ServerBindings"];
                var header = string.Format("{0}:{1}:{2}", ipAddress, port, hostname);
 
                if (bindings.Contains(header))
                    throw new InvalidOperationException("Host Header already exists!");
 
                bindings.Add(header);
                directoryEntry.CommitChanges();
            }
        }
    }
}</pre>