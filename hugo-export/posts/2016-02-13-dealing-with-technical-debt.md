---
title: Dealing with Technical Debt
author: Iulian
type: post
date: 2016-02-13T11:59:43+00:00
url: /2016/02/dealing-with-technical-debt/
categories:
  - Software development
tags:
  - Technical debt

---
<p style="text-align: justify;">
  The phrase “technical debt” has become a commonly used phrase in software development. Technical debt was introduced by Ward Cunningham to describe the cumulative consequences of corners being cut throughout a software project&#8217;s design and development.
</p>

<p style="text-align: justify;">
  Imagine you need to add a feature/functionality to your software. You see two ways of doing it, the <strong>short & quick & dirty </strong>one that will make further changes harder or the <strong>clean way</strong> that will take longer to add.
</p>

<p style="text-align: justify;">
  Technical debit is analogous to financial debt; the technical debt incurs <strong>interest</strong> payments which come in the form of extra effort you have to do in the future because of the quick and dirty design choice. We can choose to continue paying the interest, or we can pay down the <strong>principal</strong> by refactoring the quick and dirty design into the better design.
</p>

<p style="text-align: justify;">
  &#8220;Shipping first time code is like going into debt. A little debt speeds development so long as it is paid back promptly with a rewrite&#8230; The danger occurs when the debt is not repaid. Every minute spent on not-quite-right code counts as <a title="Interest" href="https://en.wikipedia.org/wiki/Interest">interest</a> on that debt. Entire engineering organizations can be brought to a stand-still under the debt load of an unconsolidated implementation, <a title="Object-oriented programming" href="https://en.wikipedia.org/wiki/Object-oriented_programming">object-oriented</a> or otherwise.&#8221; (Ward Cunningham, 1992)
</p>

<p style="text-align: center;">
  <a href="https://www.iuliantabara.com/technical-debt/" rel="attachment wp-att-1127"><img class="aligncenter size-full wp-image-1127" src="https://www.iuliantabara.com/wp-content/uploads/2016/03/technical-debt.png" alt="technical debt" width="928" height="544" srcset="https://www.iuliantabara.com/wp-content/uploads/2016/03/technical-debt.png 928w, https://www.iuliantabara.com/wp-content/uploads/2016/03/technical-debt-300x176.png 300w, https://www.iuliantabara.com/wp-content/uploads/2016/03/technical-debt-768x450.png 768w, https://www.iuliantabara.com/wp-content/uploads/2016/03/technical-debt-500x293.png 500w" sizes="(max-width: 928px) 100vw, 928px" /></a>
</p>

<p style="text-align: center;">
  <em>Technical debt trends to accumulate over time<br /> </em>
</p>

<p style="text-align: justify;">
  The time you spend below the line takes away from the time you are able to spend above the line in terms of delivering new value to the customers.
</p>

<h2 style="text-align: justify;">
  Impacts of Technical Debt
</h2>

<li style="text-align: justify;">
  Too much time maintaining existing systems, not enough time to add value &#8211; fixing defects or n * patching the existing code.
</li>
<li style="text-align: justify;">
  Takes more effort (time & money) to add new functionality &#8211; you are not able to react to what&#8217;s happening on the market in terms of competitive products.
</li>
<li style="text-align: justify;">
  User dissatisfaction &#8211; usually the functionality you&#8217;re adding is not what they expect or they struggle with the products due the amount of defects.
</li>
<li style="text-align: justify;">
  Latent defects &#8211; a defect that is not known to the customer unless he faces an unforeseen situation but at the same time the developer or the seller is aware of the defect.
</li>

## Business impact of Technical Debt

<li style="text-align: justify;">
  Higher TCO &#8211; total cost of ownership (purchase price of asset + cost of operation)
</li>
<li style="text-align: justify;">
  Longer TTM &#8211; time to market to deliver new value
</li>
<li style="text-align: justify;">
  Reduced agility &#8211; less ability to react to changes on the market
</li>

<h2 style="text-align: justify;">
  Consequences:
</h2>

<li style="text-align: justify;">
  <a href="http://www.claytonchristensen.com/books/the-innovators-dilemma/" target="_blank"><strong>Innovator&#8217;s Dilemma</strong> </a>&#8211; once dominant in the market, your competition can develop and deliver new functionality faster than you
</li>
<li style="text-align: justify;">
  studies reveal that every 1$ of competitive advantage gained by cutting quality / taking shortcuts, it costs 4 x times to restore the quality back to the product
</li>
<li style="text-align: justify;">
  <strong>The biggest consequence</strong> &#8211; it slows your ability to deliver future features, thus handing you an opportunity cost for lost revenue
</li>

## The &#8220;source&#8221; of Technical Debt

<p style="text-align: justify;">
  Technical debt is comes from work that is <em>not really Done</em> or steps skipped in order to get the product out of the door (schedule pressure).
</p>

<p style="text-align: justify;">
  A &#8220;valuable&#8221; source of &#8220;undone&#8221; work is generated by not knowing what&#8217;s required and we add code that performs functionality that&#8217;s incorrect. In this scenario we potentially try to tweak the functionality with workarounds by doing some &#8220;code manipulation&#8221;.
</p>

<p style="text-align: justify;">
  Company culture aka &#8220;that&#8217;s not my job&#8221; can sustain the technical debt (QA vs Dev vs DevOps).
</p>

<h2 style="text-align: justify;">
  Forms of Technical Debt
</h2>

<li style="text-align: justify;">
  Defects
</li>
<li style="text-align: justify;">
  Lack of automated build
</li>
<li style="text-align: justify;">
  High code complexity &#8211; hard to modify/test
</li>
<li style="text-align: justify;">
  Lack of automatic deployment &#8211; reduce human steps involvement
</li>
<li style="text-align: justify;">
  Lack of unit tests
</li>
<li style="text-align: justify;">
  Business Logic in wrong places
</li>
<li style="text-align: justify;">
  Too few acceptance tests
</li>
<li style="text-align: justify;">
  High cyclometric complexity
</li>
<li style="text-align: justify;">
  Duplicated code or modules
</li>
<li style="text-align: justify;">
  Unreadable names / algorithms
</li>
<li style="text-align: justify;">
  Highly coupled code
</li>

All forms of technical debt are associated with code modify / changes and **testing &#8211; DONE requires testing:**

  * acceptance criteria
  * details of the design/solution
  * automate all tests
  * write unit tests and code
  * review documentation
  * test integrated software
  * regression testing of existing functionality
  * fix broken code

<p style="text-align: justify;">
  It is important to take sprint capacity and perform the above steps every single sprint.
</p>

<h2 style="text-align: justify;">
  Paying Off Technical Debt
</h2>

<li style="text-align: justify;">
  Stop creating new debt
</li>
<li style="text-align: justify;">
  Make a small payment regularly (eg. each sprint)
</li>
<li style="text-align: justify;">
  Repeat step 2 🙂
</li>

**1.Stop creating new debt**

  * Clearly define what &#8220;DONE&#8221; work is!
  * Know &#8220;what&#8221; the functionality is &#8211; clear acceptance criteria
  * Automated testing 
      * repeatable, fast &#8211; do it every time the code changes!
    <li style="text-align: justify;">
      regression tests &#8211; at least every sprint to be able to run through all your regression tests and know that whatever used to work still works.
    </li>
    <li style="text-align: justify;">
      new functionalities &#8211; automatically testing so that can be very clear not only works when it&#8217;s new but next sprint when we&#8217;re modifying things around functionality, it&#8217;s still working as expected.
    </li>
<li style="text-align: justify;">
  Refactor &#8211; not rewriting the application/product but <strong>increasing the maintainability</strong> of the code without changing the behavior. It can be changes to make code readable/understandable (variable/methods name change to match the true meaning), taking our redundant code and creating a method. Fixing defects is different to refactoring. Refactoring is best done very small steps, a little bit at a time and actually requires automatic testing to make sure that the refactor has not changed the behavior. See <em>Martin Fowler &#8211; Refactoring &#8211; Improving the design of existing code</em>
</li>
<li style="text-align: justify;">
  Automated process &#8211; build & release -> eliminate human errors = reduce the source of technical debt. Let computers do repetitive work where human can easily induce errors.
</li>

<p style="text-align: justify;">
  Note: Those are some examples and not an exhaustive list of things that can generate technical debt.
</p>

## 2. Pay off existing debt

<li style="text-align: justify;">
  <strong>Fix defects</strong> &#8211; preferable as soon as you find them. Get them at the top of the list, work them off (zero bug policy), get them out of the code, refactor the code, improve the structure, make the names meaningful, reduce complexity, remove duplicate code, improve the test coverage.
</li>
<li style="text-align: justify;">
  <strong>Test coverage</strong> &#8211; it doesn&#8217;t mean only lines of code, it means testing though the logical use of the code. Test coverage is expensive so it&#8217;s most important to test the product with actually being used &#8211; the coverage we care about most because it&#8217;s how customers are using our software.
</li>

<p style="text-align: justify;">
  <a href="https://www.iuliantabara.com/technical-debt-healthy-2/" rel="attachment wp-att-1129"><img class="aligncenter size-full wp-image-1129" src="https://www.iuliantabara.com/wp-content/uploads/2016/03/technical-debt-healthy.png" alt="technical-debt-healthy" width="930" height="572" srcset="https://www.iuliantabara.com/wp-content/uploads/2016/03/technical-debt-healthy.png 930w, https://www.iuliantabara.com/wp-content/uploads/2016/03/technical-debt-healthy-300x185.png 300w, https://www.iuliantabara.com/wp-content/uploads/2016/03/technical-debt-healthy-768x472.png 768w, https://www.iuliantabara.com/wp-content/uploads/2016/03/technical-debt-healthy-488x300.png 488w" sizes="(max-width: 930px) 100vw, 930px" /></a><a href="https://www.iuliantabara.com/2016/02/dealing-with-technical-debt/technical-debt-healthy/" rel="attachment wp-att-1115"><br /> </a>The goal: to pay the tech debt a little bit at a time every single sprint and avoid &#8220;technical bankruptcy&#8221;!
</p>

## 3. Prevent tech debt

  * cross-functional teams
  * agreed upon definition of done
  * frequent user feedback
  * discipline, transparency &#8211; not taking shortcuts

<p style="text-align: justify;">
  Additional links:
</p>

<li style="text-align: justify;">
  <a href="http://martinfowler.com/bliki/TechnicalDebt.html" target="_blank">http://martinfowler.com/bliki/TechnicalDebt.html</a>
</li>
<li style="text-align: justify;">
  <a href="http://martinfowler.com/books/refactoring.html" target="_blank">http://martinfowler.com/books/refactoring.html</a>
</li>
<li style="text-align: justify;">
  <a href="https://www.atlassian.com/agile/technical-debt">https://www.atlassian.com/agile/technical-debt</a>
</li>
<li style="text-align: justify;">
  <a href="http://blogs.ripple-rock.com/SteveGarnett/2013/03/05/TechnicalDebtStrategiesTacticsForAvoidingRemovingIt.aspx">http://blogs.ripple-rock.com/SteveGarnett/2013/03/05/TechnicalDebtStrategiesTacticsForAvoidingRemovingIt.aspx</a>
</li>