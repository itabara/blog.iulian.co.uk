---
title: Create a new process that will not be my child process ?
author: Iulian
type: post
date: 2009-07-30T20:34:00+00:00
url: /2009/07/create-a-new-process-that-will-not-be-my-child-process/
categories:
  - Source code
tags:
  - WIN32

---
<p align="justify">
  Is there a way to create a new process that will not be my child process?<br />Meaning, that after opening the new process, if I’ll open TaskManager and do “End Process Tree” on the parent process, it will not terminate the child process as well?
</p>

Below the code: 

<pre class="lang:c# decode:true ">using System;
using System.Management;
 
namespace TestDettachedProcess
{
    class Program
    {
        static void Main(string[] args)
        {
            ManagementBaseObject inArgs = null;
            ManagementBaseObject outArgs = null;
            using (ManagementClass mc = new ManagementClass("Win32_Process"))
            {
                ManagementClass si = new
                ManagementClass("Win32_ProcessStartup");
                si.Properties["CreateFlags"].Value = 8;
                si.Properties["ShowWindow"].Value = 1;
                inArgs = mc.GetMethodParameters("Create");
                inArgs["CommandLine"] = "notepad.exe";
                inArgs["ProcessStartupInformation"] = si;
                outArgs = mc.InvokeMethod("Create", inArgs, null);
                uint ret =
                Convert.ToUInt32(outArgs.Properties["ReturnValue"].Value);
                if (ret != 0)
                    Console.WriteLine("Failed : {0}", ret);
                else
                    Console.WriteLine("Success : {0}",
                    outArgs.Properties["ProcessId"].Value);
            }
        }
    }
}</pre>

<p align="justify">
  Here I create a new process (starting notepad) detached from any other process (CreateFlags = 8).
</p>

<p align="justify">
  You can also set other values, for instance if you want the new process to become a new process group parent the you have to set CreateFlags = 512 + 8. Search the WMI docs in MSDN for the details about the possible <a href="http://msdn.microsoft.com/en-us/library/aa394372%28VS.85%29.aspx" target="_blank">Win32_Process</a> and <a href="http://msdn.microsoft.com/en-us/library/aa394375%28VS.85%29.aspx" target="_blank">Win32_ProcessStartup</a> properties.
</p>