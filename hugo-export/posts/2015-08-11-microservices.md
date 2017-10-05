---
title: Microservices
author: Iulian
type: post
date: 2015-08-11T20:11:39+00:00
url: /2015/08/microservices/
categories:
  - Architecture
tags:
  - microservices

---
# Intro

<p style="text-align: justify;">
  Back in the day most systems started as a monolithic system, which basically means the entire application exists in a single code base and is deployed and, importantly, scaled as a single unit.
</p>

<p style="text-align: justify;">
  A monolithic application puts all its functionality into a single process&#8230; and scales by replicating the monolith on multiple servers. A monolithic application is harder to scale, as one small change must be deployed across all processes.
</p>

# What are microservices ?

<p style="text-align: justify;">
  According to <a href="https://en.wikipedia.org/wiki/Microservices" target="_blank">wikipedia</a>, <b>microservices</b> are a <a title="Architectural pattern" href="https://en.wikipedia.org/wiki/Architectural_pattern">software architecture style</a> in which complex <a class="mw-redirect" title="Software application" href="https://en.wikipedia.org/wiki/Software_application">applications</a> are composed of small, independent <a title="Process (computing)" href="https://en.wikipedia.org/wiki/Process_%28computing%29">processes</a> communicating with each other using <a title="Language-independent specification" href="https://en.wikipedia.org/wiki/Language-independent_specification">language-agnostic</a> <a title="Application programming interface" href="https://en.wikipedia.org/wiki/Application_programming_interface">APIs</a><sup>.</sup> These <a title="Service (systems architecture)" href="https://en.wikipedia.org/wiki/Service_%28systems_architecture%29">services</a> are small, highly <a title="Coupling (computer programming)" href="https://en.wikipedia.org/wiki/Coupling_%28computer_programming%29">decoupled</a> and focus on doing a small task, facilitating a <a title="Modularity" href="https://en.wikipedia.org/wiki/Modularity">modular</a> approach to <a title="System" href="https://en.wikipedia.org/wiki/System">system</a>-building.
</p>

<p style="text-align: justify;">
  <a href="http://martinfowler.com/articles/microservices.html">Software developer advocates James Lewis and Martin Fowler</a> gave the best definition we could find for this increasingly popular architectural pattern:
</p>

> <p style="text-align: justify;">
>   “The microservice architectural style is an approach to developing a single application as a suite of small services, each running in its own process and communicating with lightweight mechanisms, often an HTTP resource API. These services are built around business capabilities and independently deployable by fully automated deployment machinery. There is a bare minimum of centralized management of these services, which may be written in different programming languages and use different data storage technologies.”
> </p>

<p style="text-align: justify;">
  A microservices architecture puts each element of functionality into a separate service&#8230;and scales by distributing these services across servers, replicating as needed.
</p>

<h1 style="text-align: justify;">
  Characteristics of microservices
</h1>

  * Small with a single responsibility
  * Each application does one thing
  * Small enough to fit your head &#8211; If a class is bigger than by head then it is too big
  * Small enough that you can throw them away &#8211; Rewrite over Maintain
  * Status aware and Auto scaling &#8211; multiple consumers (Competing Consumers pattern <a href="http://www.enterpriseintegrationpatterns.com/CompetingConsumers.html" target="_blank">example</a>
  * WatchDog process to monitor app-status (expose metrics about itself eg: queue dept) auto-scale to meet throughput requirements.
  * A single capability composed of a small number of activities and exposing a uniform interface (REST)
  * Each is entirely decoupled from it&#8217;s clients, scalable, testable and deployable individually.

#### How does a microservice outshine a monolith? {#howdoesamicroserviceoutshineamonolith}

  1. It makes your app more scalable with smaller teams.
  2. Enables modularity and is more open to change in general.
  3. Uses a smaller codebase.

#### What are microservice architecture’s limitations? {#whataremicroservicearchitecture’slimitations}

  1. It can cause communication problems among different parts of the app and within your team.
  2. It requires increased effort later on to catalog, test, and fix incompatibilities.
  3. You should still build the monolith before building the microservices on top of it.