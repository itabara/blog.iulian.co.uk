---
title: 'Private: Liskov Substitution Principle'
author: Iulian
type: post
date: 2015-05-04T16:30:09+00:00
draft: true
private: true
url: /2015/05/liskov-substitution-principle/
categories:
  - Software development

---
<p style="text-align: justify;">
  The third SOLID principle is the Liskov Substitution Principle which is described as “Functions that use pointers or references to base classes must be able to use objects or derived classes without knowing it”.<br /> That means that when our code uses a specific class or interface it should be able to use a derived class or different implementation of the interface without having to change its internal behavior. This will allow you to reduce the code fragility.
</p>

<p style="text-align: justify;">
  When first learning about object oriented programming, inheritance is usually described as an &#8220;is a&#8221; relationship. If a penguin &#8220;is a&#8221; bird, then the <code>Penguin</code> class should inherit from the <code>Bird</code> class. The &#8220;is a&#8221; technique of determining inheritance relationships is simple and useful, but occasionally results in bad use of inheritance.
</p>

 class Bird {
  
public:
  
virtual void setLocation(double longitude, double latitude) = 0;
  
virtual void setAltitude(double altitude) = 0;
  
virtual void draw() = 0;
  
};

void Penguin::setAltitude(double altitude)
  
{
  
//altitude can&#8217;t be set because penguins can&#8217;t fly
  
//this function does nothing
  
}

**If an override method does nothing or just throws an exception, then you&#8217;re probably violating the LSP.**

<p style="text-align: center;">
  <a href="http://www.iuliantabara.com/wp-content/uploads/2015/05/lsp.jpg"><img class="aligncenter size-full wp-image-575" src="http://www.iuliantabara.com/wp-content/uploads/2015/05/lsp.jpg" alt="lsp" width="750" height="600" srcset="https://www.iuliantabara.com/wp-content/uploads/2015/05/lsp.jpg 750w, https://www.iuliantabara.com/wp-content/uploads/2015/05/lsp-300x240.jpg 300w" sizes="(max-width: 750px) 100vw, 750px" /></a>Source: <a href="https://lostechies.com/derickbailey/2009/02/11/solid-development-principles-in-motivational-pictures/" target="_blank">Solid motivational pictures</a>
</p>

<p style="text-align: left;">
  While a penguin <em>technically</em> &#8220;is a&#8221; bird, the <code>Bird</code> class makes the assumption that all birds can fly. Because the <code>Penguin</code> subclass violates the flying assumption, it does not satisfy the Liskov Substitution Principle for the <code>Bird</code> superclass.
</p>

<p style="text-align: left;">
  class Bird {<br /> public:<br /> virtual void draw() = 0;<br /> virtual void setLocation(double longitude, double latitude) = 0;<br /> };
</p>

class FlightfulBird : public Bird {
  
public:
  
virtual void setAltitude(double altitude) = 0;
  
};

<p style="text-align: left;">
  In the above solution the <code>Bird</code> base class does not contain any flying functionality, and the <code>FlightfulBird</code> subclass adds that functionality. This allows some functions to be applied to both <code>Bird</code> and <code>FlightfulBird</code> objects; drawing for example. However, the <code>Bird</code> objects, which may be flightless, can not be shoved into functions that take <code>FlightfulBird</code> objects.
</p>