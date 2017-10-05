---
title: Single Responsibility Principle
author: Iulian
type: post
date: 2015-05-02T17:42:40+00:00
url: /2015/05/single-responsibility-principle/
categories:
  - Software development
tags:
  - loose coupling
  - SOLID
  - SRP

---
<p align="justify">
  Let’s start our journey on <a href="http://www.iuliantabara.com/2015/05/the-solid-principles/" target="_blank">SOLID principles</a> by describing the Single Responsibility Principle (SRP).
</p>

<p align="justify">
  SRP states that each class should have one responsibility only and, therefore, only one reason to change. It means that every class should have one job to do or one single purpose. Of course, we still allow adding multiple methods/properties and members as long they relate to that single responsibility.
</p>

<p align="justify">
  Applying SRP will change your existing code and the first impact will be at the class level: the classes in your projects will become smaller and cleaner. As a note, you shouldn&#8217;t be worried about changing the classes even you will notice the increased number of new classes. All major programming languages allows you to organize classes using namespaces and project folders. Keeping your code into classes tightly focused into a single purpose leads to code that is simpler to understand and maintain.
</p>

<p align="justify">
  Having small and cohesive classes reduces the code fragility by decreasing the chance of a class to contain bugs (reducing the need for changes). As the classes will perform only one duty, multiple classes will work together to achieve larger tasks. It allows you to easy modify the features, either by extending existing classes or introducing interchangeable versions.
</p>

<p align="justify">
  Along with the other principles, SRP allows you to achieve <strong>loose coupling</strong>.
</p>

<p style="text-align: center;">
  <a href="http://www.iuliantabara.com/wp-content/uploads/2015/05/srp1.jpg"><img class="aligncenter size-full wp-image-567" src="http://www.iuliantabara.com/wp-content/uploads/2015/05/srp1.jpg" alt="srp" width="750" height="600" srcset="https://www.iuliantabara.com/wp-content/uploads/2015/05/srp1.jpg 750w, https://www.iuliantabara.com/wp-content/uploads/2015/05/srp1-300x240.jpg 300w" sizes="(max-width: 750px) 100vw, 750px" /></a>Source: <a href="https://lostechies.com/derickbailey/2009/02/11/solid-development-principles-in-motivational-pictures/" target="_blank">Solid motivational pictures</a>
</p>

<p align="justify">
  <strong>Example:</strong>
</p>

<p align="justify">
  Let’s consider below code snippet that violates the SRP principle and then refactor it to comply with the principle.
</p>

<pre class="brush: csharp;">using System;
namespace SingleResponsibilityPrinciple.BadCode
{
    public class GoldMeter
    {
        public double GoldPrice { get; set; }

        public void ReadGoldPrice()
        {
            // for demo purposes let's consider a random value
            var r = new Random();
            // min price 100 + 365 days 🙂
            GoldPrice = 100 + r.NextDouble() * 365;
        }

        public bool IsGoldPriceLow()
        {
            return GoldPrice &lt;= 120;
        }

        public void ShowLowGoldPriceAlert()
        {
            Console.WriteLine("Better to buy some gold now: {0:F2}", GoldPrice);
        }
    }
}
</pre>

<p align="justify">
  The above class will read gold price from an independent trusted “source” and check if the price is under certain threshold and it will decide to alert me if I need to buy some gold.
</p>

<p align="justify">
  There are at least few reasons to change the above class. Let’s consider at some point I need to add a new “trusted” independent source for gold price. Perhaps I want to change the way I’m evaluating the opportunity to buy gold. Or I would like a more sophisticated system to be notified instead of logging out to console.
</p>

<p align="justify">
  To refactor the code, we’ll separate the functionality into three classes. The first is <strong>GoldMeter.cs</strong> that will retain <em>GoldPrice</em> property and <em>ReadGoldPrice</em> method as both are close related. The other methods are removed so that the only reason to change is the replacement of the “trusted” source price.
</p>

<p align="justify">
  The second class is <strong>GoldPriceChecker.cs</strong> that will include a very simple method to compare the price with a certain acceptable value. The only reason to change the class is if the acceptable value is changed.
</p>

<p align="justify">
  The final class <strong>GoldPriceAlert.cs</strong> will display the alert if a very simple way. GoldMeter class is dependency injected. The only reason to change the GoldPriceAlert class is if we decide to enhance the alert system.
</p>

<pre class="brush: csharp;">using System;
namespace SingleResponsibilityPrinciple.RefactoredCode
{
    public class GoldMeterEnchanced
    {
        public double GoldPrice { get; set; }

        public void ReadGoldPrice()
        {
            // for demo purposes let's consider a random value
            var r = new Random();
            // min price 100 + 365 days 🙂
            GoldPrice = 100 + r.NextDouble() * 365;
        }
    }

    public class GoldPriceChecker
    {
        private double myMaxGoldPrice = 120;
        public bool IsGoldPriceLow(GoldMeterEnchanced goldmeter)
        {
            return goldmeter.GoldPrice &lt;= myMaxGoldPrice;
        }
    }

    public class GoldPriceAlert
    {
        public void ShowLowGoldPriceAlert(GoldMeterEnchanced goldmeter)
        {
            Console.WriteLine("Better to buy some gold now: {0:F2}", goldmeter.GoldPrice);
        }
    }
}
</pre>

<p align="justify">
  The refactored code below breaks some SOLID principles just to ensure the SRP is visible. Further refactoring of the code is required to achieve SOLID compliance.
</p>