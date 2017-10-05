---
title: Open / Closed Principle
author: Iulian
type: post
date: 2015-05-03T19:07:39+00:00
url: /2015/05/open-closed-principle/
categories:
  - Software development
tags:
  - loose coupling
  - robust software
  - SOLID

---
<p align="justify">
  The Open/Closed Principle (OCP) states that all classes should be open for extension but closed for modification. “Open for extension” means that the design of your class should allow adding new functionality as new requirements are generated. “Closed for modification” means that once you have developed the class, you should never modify it, unless you need to fix bugs.
</p>

<p align="justify">
  Even the two parts of the principle appear to be opposite, if you structure your code correctly, you should be able to add functionality without editing existing code. How you achieve this ? Your design should reply on abstractions for dependencies, such as <strong>interfaces</strong> or <strong>abstract classes</strong>, rather than using concrete implementation. New functionality can be added by adding new classes than implement the interfaces.
</p>

<p align="justify">
  The first benefit of OCP to your code is that you don’t need to change the existing code, once it was written, tested, documented. So you reduce the risk on adding bugs into existing code. Another benefit of using contracts is loose coupling and increased flexibility.
</p>

<p style="text-align: center;" align="justify">
  <a href="http://www.iuliantabara.com/wp-content/uploads/2015/05/ocp.jpg"><img class="aligncenter size-full wp-image-571" src="http://www.iuliantabara.com/wp-content/uploads/2015/05/ocp.jpg" alt="ocp" width="750" height="600" srcset="https://www.iuliantabara.com/wp-content/uploads/2015/05/ocp.jpg 750w, https://www.iuliantabara.com/wp-content/uploads/2015/05/ocp-300x240.jpg 300w" sizes="(max-width: 750px) 100vw, 750px" /></a>Source: <a href="https://lostechies.com/derickbailey/2009/02/11/solid-development-principles-in-motivational-pictures/" target="_blank">Solid motivational pictures</a>
</p>

<p align="justify">
  Let’s consider the GoldPriceAlert class from previous article with the extra feature to allow user to decide the way to be notified: console, email, sms.
</p>

<pre class="brush: csharp;">using System;
using SingleResponsibilityPrinciple.RefactoredCode;

namespace OpenClosedPrinciple.BadCode
{
    public class GoldPriceAlert
    {
        public void ShowLowGoldPriceAlert(GoldMeterEnchanced goldmeter, LogType logType)
        {
            if (logType == LogType.Email)
            {
                // TODO: Send me the email
                return;
            }

            Console.WriteLine("Better to buy some gold now: {0:F2}", goldmeter.GoldPrice);
        }
    }

    public enum LogType
    {
        Console,
        Email,
        SMS
    }
}
</pre>

<p align="justify">
  The above sample code is a basic module for logging alerts. As you can see if you decide to implement the SMS logging type, you need to modify the <strong>GoldPriceAlert</strong> method. This violates the OCP.
</p>

<p align="justify">
  We can easily refactor the code to achieve the OCP by removing the enum <strong>LogType</strong> that limits the types of alerts. Then we’ll create a class for each type of logger. Additional log types can be added later without changing any existing code.
</p>

<p align="justify">
  The logger class still performs all logging but using one of the classes described earlier. In order that the classes are loose coupled, each message logger type implements <strong>ILog</strong> interface. The <strong>GoldPriceAlert </strong>class is never aware of the type of the logger being used as the dependency is provided as an <strong>ILog</strong> instance using <strong>constructor injection</strong>.
</p>

The refactored code:

<pre class="brush: csharp;">using SingleResponsibilityPrinciple.RefactoredCode;
using System;

namespace OpenClosedPrinciple.RefactoredCode
{
    public interface ILog
    {
        void LogAlert(string message);
    }

    public class Log
    {
        ILog logger;

        public Log(ILog logger)
        {
            this.logger = logger;
        }

        public void LogAlert(string message)
        {
            logger.LogAlert(message);
        }
    }

    public class ConsoleAlert : ILog
    {

        public void LogAlert(string message)
        {
            Console.WriteLine(message);
        }
    }

    public class EmailAlert : ILog
    {

        public void LogAlert(string message)
        {
            //TODO: Send me the email
        }
    }

    public class SMSAlert : ILog
    {

        public void LogAlert(string message)
        {
            //TODO: Send me the SMS
        }
    }

    public class GoldPriceAlert
    {
        public void ShowLowGoldPriceAlert(GoldMeterEnchanced goldmeter, ILog logger)
        {
            var message = string.Format("Better to buy some gold now: {0:F2}", goldmeter.GoldPrice);
            logger.LogAlert(message);
        }
    }
}
</pre>