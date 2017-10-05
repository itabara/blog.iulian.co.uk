---
title: The Solid principles
author: Iulian
type: post
date: 2015-05-02T12:50:13+00:00
url: /2015/05/the-solid-principles/
categories:
  - Software development
tags:
  - fragile code
  - rigid code
  - SOLID

---
The SOLID acronym was introduced by **Robert Martin **(also known as “Uncle Bob” and represents a list of five guidelines that can enhance the maintainability of the software.

<p align="justify">
  Bad dependency management will make your code rigid, fragile, difficult to reuse. Rigid code is that which is difficult to modify, either to change existing functionality or add new features. Fragile code is susceptible to the introduction of bugs, particularly those that appear in a module when another code is changed.
</p>

<p align="justify">
  If you follow SOLID principles, you can produce better and flexible code, more robust and with a higher reusability.
</p>

<p align="justify">
  The 5 principles:
</p>

<p align="justify">
  <strong>1. Single Responsibility Principle (SRP)</strong> – each class should have one responsibility and, therefore, only one reason to change.
</p>

<p align="justify">
  <strong>2. Open / Closed Principle (OCP)</strong> – all classes and similar units of source code should be open for extension but close to modification.
</p>

<p align="justify">
  <strong>3. Liskov Substitution Principle (LSP)</strong> – code that uses a base class must be able to substitute a subclass without knowing it.
</p>

<p align="justify">
  <strong>4. Interface Segregation Principle (ISP)</strong> – the client should not be forced to depend upon interfaces that they do not use. Instead, those interfaces should be minimized.
</p>

<p align="justify">
  <strong>5. Dependency Inversion Principle (DIP)</strong> – high level modules should depend upon low level modules and that abstractions should not depend upon details.
</p>