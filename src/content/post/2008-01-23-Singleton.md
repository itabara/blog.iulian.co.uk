---
title: 'Design Patterns - Creational - Singleton'
author: Iulian
date: 2008-01-23T12:11:25+00:00
categories:
  - Design Patterns
tags:
  - patterns
  - architecture

---
# Singleton

Ensure that only one instance of the class is created and provide a global access point to the object.

Singleton pattern is the simplest design pattern: it involves only one class which is responsible to instantiate itself, to make sure it creates not more than one instance; in the same time it provides a global point of access to that instance. In this case the same instance can be used from everywhere, being impossible to invoke directly the constructor each time.

## When to use

Singleton pattern should be used when we must ensure that only one instance of a class is created and when the instance must be available through all the code. You must pay attention when dealing with multithreading environments when multiple threads must access the same resource through the same singleton object.

## Common usage

There are few common scenarious when Singleton pattern is used:

- Configuration classes
- Logger classes
- Accesing shared resouces
- In conjunction with other design patterns: Factories and Abstract Factories, Builder, Prototype

## Implementation

See on https://github.com/itabara/designpatterns/singleton
