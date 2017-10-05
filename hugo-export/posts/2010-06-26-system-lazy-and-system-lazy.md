---
title: 'System.Lazy<T> and System.Lazy'
author: Iulian
type: post
date: 2010-06-26T11:17:36+00:00
url: /2010/06/system-lazy-and-system-lazy/
categories:
  - .Net Framework

---
<p align="justify">
  One of the cool new feature inside the .Net 4.0 framework is <a href="http://msdn.microsoft.com/en-us/library/dd642331%28VS.100%29.aspx">System.Lazy and System.Lazy<T></a>. What System.Lazy brings to the table is a way to create objects which may need to perform intensive operations and defer the execution of the operation until it is 100% absolutely needed.
</p>

<p align="justify">
  Lazy initialization occurs the first time the<strong> Lazy.Value</strong> property is accessed or the <strong>Lazy.ToString()</strong> method is called.
</p>

<p align="justify">
  <strong>HostingAccount.cs</strong>
</p>

<pre class="lang:c# decode:true ">public class HostingAccount
{
    public string CustomerId { get; set; }
    public string PlanName { get; set; }
    public string Domain { get; set; }

    public HostingAccount(string CustomerId, string PlanName, string Domain)
    {
        this.CustomerId = CustomerId;
        this.PlanName = PlanName;
        this.Domain = Domain;
    }

    public HostingAccount()
    {

    }
}</pre>

**HostingAccountFactory.cs**

<pre class="lang:c# decode:true ">public class HostingAccountFactory
{
    public HostingAccount GetMasterAccount()
    {
        Console.WriteLine("About to fetch master account...");

        // This can be a long time operation like reading from AD
        return new HostingAccount {CustomerId = "1", Domain = "Domain_1", PlanName = "Plan_1"};
    }

    public List&lt;HostingAccount&gt; FetchAccounts()
    {
        Console.WriteLine("About to fetch hosting accounts...");

        // This can be a long time operation like reading from AD
        return new List&lt;HostingAccount&gt;
        {
            new HostingAccount("1", "Domain_1", "Plan_1"),
            new HostingAccount{CustomerId = "2", Domain = "Domain_2", PlanName = "Plan_2"},
            new HostingAccount{CustomerId = "3", Domain = "Domain_3", PlanName = "Plan_3"},
            new HostingAccount{CustomerId = "4", Domain = "Domain_4", PlanName = "Plan_4"}
        };
    }
}</pre>

&nbsp;

<pre class="lang:c# decode:true ">public class Program
{
    static void Main(string[] args)
    {
        var hostingAccountFactory = new HostingAccountFactory();

        Console.WriteLine("Fetching hosting accounts --- non lazy");

        var hostingAccountNonLazy = hostingAccountFactory.GetMasterAccount();
        var hostingAccountListNonLazy = hostingAccountFactory.FetchAccounts();

        Console.WriteLine(Environment.NewLine);

        Console.WriteLine("Fetching hosting accounts --- lazy");

        var hostingAccountLazy = new Lazy&lt;HostingAccount&gt;     (hostingAccountFactory.GetMasterAccount);
        var hostingAccountListLazy = new Lazy&lt;List&lt;HostingAccount&gt;&gt;(() =&gt; { return hostingAccountFactory.FetchAccounts(); });

        Console.WriteLine( "Fetching hosting account --- lazy -- created lazy objects...." );
        Console.WriteLine("Has Data been fetched for List? - {0}", hostingAccountListLazy.IsValueCreated);
        Console.WriteLine("Has Data been fetched for HostingAccount ? - {0}", hostingAccountLazy.IsValueCreated);

        Console.WriteLine( "Fetching hosting account --- lazy -- using lazy objects...." );
        var hostingList = hostingAccountListLazy.Value;
        var hosting = hostingAccountLazy.Value;

        Console.WriteLine("Has Data been fetched for List? - {0}", hostingAccountListLazy.IsValueCreated);
        Console.WriteLine("Has Data been fetched for HostingAccount ? - {0}", hostingAccountLazy.IsValueCreated);
    }
}</pre>

As you can see using Lazy is easy and can be a very powerful tool in your tool chest. But like everything else, this feature of the framework is NOT meant for ever scenario, use it where it makes sense.