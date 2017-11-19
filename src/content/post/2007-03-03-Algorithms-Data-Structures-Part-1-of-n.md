---
title: 'Algorithms & Data Structures – Part 1 of N'
author: Iulian
type: post
date: 2007-03-03T13:48:32+00:00
url: /2007/03/algorithms-data-structures-part-1-of-n/
categories:
  - Algorithms
tags:
  - algorithms
  - complexity

---
# Introduction

When we create/analyse an algorithm, we should keep in mind at least three aspects:

* What we're trying to achieve - formalize
* Correctness
* Efficiency (time, memory usage, power consumption)

How to determine how much time an algorithm A uses to solve problem X ?

* Depends on input (input size "n" as parameter)
* Determine function f(n) representing cost.

**Empirical analysis:** code is and use timer running on many inputs.

**Algorithm analysis:** analyze steps of algorithm, estimating amount of work each step takes.

## Big O Notation
In theoretical analysis of algorithms it is common to estimate their complexity in the asymptotic sense, i.e., to estimate the complexity function for arbitrarily large input. [Big O notation](https://en.wikipedia.org/wiki/Big_O_notation), [Big-omega notation](https://en.wikipedia.org/wiki/Big-omega_notation), [Big-theta notation](https://en.wikipedia.org/wiki/Big-theta_notation) are used to this end.

**Rate of growth:** measure how quickly the graph of function rises.

* **T(n) = O(f(n))** *(Big-oh)* means that the growth rate of **T(n)** is asymptotically **less than or equal to** the growth rate of **f(n)**. *Lingo: T(n) grows no faster than f(n)*.

* **T(n) = Ω(f(n))** *(Omega)* means that the growth rate of **T(n)** is asymptotically **greater than or equal to** the growth rate of **f(n)**. *Lingo: T(n) grows no faster than f(n).*

* **T(n) = Θ(f(n))** *(Theta)* means that the growth rate of **T(n)** is asymptotically **equal to** the growth rate of **f(n)**.

## Hierarchy of Big-oh

Functions ranked in increasing order of growth:

* 1
* log log n
* log n
* n
* n log n
* n <sup>2</sup>
* n <sup>2</sup> log n
* n <sup>3</sup>
* ...
* 2<sup>n</sup>
* n!
* n<sup>n</sup>

Program loop evaluation:

* **O(n)** &#8211; Adding to the loop counter means that the loop runtime grows linearly when compared to it's maximum value n.

```csharp
for (int i = 0; i < n; i += c) // O(n)
    statement(s);
```

* **O(log n)** - Multiplying the loop counter means that the maximum value n must grow exponentially to linearly increase the loop runtime - therefore it is logarithmic. The loop executes it&'s body exactly **log<sub>c</sub>n** times.

```csharp
for (int i = 0; i < n; i *= c) // O(log n)
    statement(s);
```

* **O(n<sup>2</sup>)** - The loop maximum is n<sup>2</sup> so the runtime is quadratic. Loop executes it's body exactly (n<sup>2</sup> / c) times.


```csharp
for (int i = 0; i < n * n; i += c)
    statement(s);
```

* **O(n<sup>2</sup>)** - Nesting loops multiplies their runtimes.
```csharp
for (int i = 0; i < n; i += c){
   for (int j = 0; j < n; i += c){
       statement;
   }
}
```

* **O(n) / O (n log n)** - loops in sequence add together their runtimes, which means the loop set with the larger runtime dominates:

```csharp
for (int i = 0; i < n; i += c) {         // O(n)
   statement;
}
```

```csharp
for (int i = 0; i < n; i += c) {         // O(n log n)
    for (int j = 0; j < n; i *= c){
        statement;
    }
}
```

Additional details:

* [https://en.wikipedia.org/wiki/Big_O_notation](href="https://en.wikipedia.org/wiki/Big_O_notation)
