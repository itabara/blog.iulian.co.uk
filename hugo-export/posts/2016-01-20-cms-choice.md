---
title: CMS choice
author: Iulian
type: post
date: 2016-01-20T12:28:53+00:00
url: /2016/01/cms-choice/
categories:
  - Web development
tags:
  - CMS
  - Craft
  - ExpressionEngine

---
<p style="text-align: justify;">
  I&#8217;m looking for a PHP based CMS, easy to scale and flexible enough to be the starting point for the next generation of e-commerce website for the hosting company I work for.
</p>

<p style="text-align: justify;">
  Why PHP ? I started by adding scalability on top and I felt nodejs was the answer. However things are not pretty generous on CMS side. There are few names but they look at the early stage of development or suffering from the lack of features: <a href="http://keystonejs.com/" target="_blank">KeystoneJS</a>, <a href="https://pencilblue.org/" target="_blank">Pencilblue, </a><a href="http://apostrophenow.org/" target="_blank">Apostrophe, </a><a href="https://ghost.org/" target="_blank">Ghost.</a>
</p>

<p style="text-align: justify;">
  Note: <a href="https://reactioncommerce.com/" target="_blank">Reaction Commerce</a> &#8211; is a really interesting project as they are the only one building something for the <strong>eCommerce</strong> industry. They are using Meteor, Node.js (note: interesting combination), MongoDB and CoffeScript and it is launched as a Docker container.
</p>

<p style="text-align: justify;">
  I&#8217;m going to collect the strengths and weaknesses of few PHP options available with the mention I&#8217;m going to write a separate post for Reaction Commerce.
</p>

<p style="text-align: justify;">
  We&#8217;ll discuss about:
</p>

<li style="text-align: justify;">
  Expression Engine
</li>
<li style="text-align: justify;">
  Craft
</li>
<li style="text-align: justify;">
  ProcessWire
</li>

<p style="text-align: justify;">
  <strong>ExpressionEngine</strong> is built by <a href="https://ellislab.com/">EllisLab</a>, a company that also created <a href="https://ellislab.com/codeigniter/">CodeIgniter</a>, a popular PHP framework for building robust web applications. ExpressionEngine 2.x is built on top of CodeIgniter.
</p>

<p style="text-align: justify;">
  <strong>Craft</strong> is built by <a href="http://pixelandtonic.com/">Pixel and Tonic</a>, a company who, interestingly, got started creating third-party add-ons <em>for</em> ExpressionEngine. Their add-ons – <a href="http://pixelandtonic.com/playa/">Playa</a> and <a href="http://pixelandtonic.com/matrix/">Matrix</a> – are well-built, renown plugins within the ExpressionEngine community.
</p>

<p style="text-align: justify;">
  <strong><a href="https://processwire.com/" target="_blank">ProcessWire</a> </strong>&#8211; It&#8217;s basically PHP with a really extensive jQuery-like API &#8211; so literally anything is possible.
</p>

<h2 style="text-align: justify;">
  Data modelling
</h2>

<p style="text-align: justify;">
  A model is simply a type of content your site stores. You might have a “blog post”, “product”, or “staff member” model. ExpressionEngine calls these model types a <a href="https://ellislab.com/expressionengine/user-guide/add-ons/channel/channel_entries.html">channel</a> while Craft calls them a <a href="http://buildwithcraft.com/docs/sections-and-entries#sections">section.</a>
</p>

<p style="text-align: justify;">
  Flexibility of the model by custom fields.
</p>

## Craft

  * Responsive control panel
  * Live preview
  * Entry draft/version functionality
  * Has [several pricing options][1] to fit your needs
  * [Custom entry types][2] (if you have several “types” of blog posts that differ in content/layout)

## ExpressionEngine

  * More add-ons for things like e-commerce
  * Been around longer
  * Well known within large companies

Craft uses [Twig][3] as its template engine.

<p style="text-align: justify;">
  Additional links:
</p>

  1. <a href="https://craftcms.com/" target="_blank">https://craftcms.com/</a>
  2. <a href="https://ellislab.com/expressionengine" target="_blank">https://ellislab.com/expressionengine</a>

 [1]: http://buildwithcraft.com/pricing
 [2]: http://buildwithcraft.com/docs/sections-and-entries#entry-types
 [3]: http://twig.sensiolabs.org/