---
title: The S.O.L.I.D. Principles
author: Iulian
type: post
date: 2010-03-20T19:58:06+00:00
url: /2010/03/the-s-o-l-i-d-principles/
categories:
  - Design patterns
tags:
  - Patterns

---
The S.O.L.I.D. design principles are a collection of best practices for object-oriented design. All of the Gang of Four design patterns adhere to these principles in one form or another. The term S.O.L.I.D. comes from the initial letter of each of the five principles that were collected in the book Agile Principles, Patterns, and Practices in C# by Robert C. Martin, or Uncle Bob to his friends.

**1. Single Responsibility Principle (SRP)**
  
The principle of SRP is closely aligned with SOC (Separation of Concerns). It states that every object should only have one
  
reason to change and a single focus of responsibility. By adhering to this principle, you avoid the problem of monolithic class design that is the software equivalent of a Swiss army knife. By having concise objects, you again increase the readability and maintenance of a system.

**2. Open-Closed Principle (OCP)**
  
The OCP states that classes should be open for extension and closed for modification, in that you should be able to add new features and extend a class without changing its internal behavior. The principle strives to avoid breaking the existing class and other classes that depend on it, which would create a ripple effect of bugs and errors throughout your application.

**3. Liskov Substitution Principle (LSP)**
  
The LSP dictates that you should be able to use any derived class in place of a parent class and have it behave in the same manner without modification. This principle is in line with OCP in that it ensures that a derived class does not affect the behavior of a parent class, or, put another way, derived classes must be substitutable for their base classes.

**4. Interface Segregation Principle (ISP)**
  
The ISP is all about splitting the methods of a contract into groups of responsibility and assigning interfaces to these groups to prevent a client from needing to implement one large interface and a host of methods that they do not use. The purpose behind this is so that classes wanting to use the same interfaces only need to implement a specific set of methods as opposed to a monolithic interface of methods.

**5. Dependency Inversion Principle (DIP)**
  
The DIP is all about isolating your classes from concrete implementations and having them depend on abstract classes or interfaces. It promotes the mantra of coding to an interface rather than an implementation, which increases flexibility within a system by ensuring you are not tightly coupled to one implementation.