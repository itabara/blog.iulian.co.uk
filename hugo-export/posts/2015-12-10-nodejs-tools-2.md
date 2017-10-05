---
title: Node.JS Tools
author: Iulian
type: post
date: 2015-12-10T12:59:17+00:00
url: /2015/12/nodejs-tools-2/
categories:
  - Node.JS
tags:
  - NodeJS

---
## 1. NPM &#8211; The Node Package Manager {#npm-the-node-package-manager}

<p style="text-align: justify;">
  When discussing Node.js, one thing that definitely should not be omitted is built-in support for package management using the <a href="https://www.npmjs.com/" target="_blank">NPM</a> tool that comes by default with every Node.js installation. The idea of NPM modules is a set of publicly available, reusable components, available through easy installation via an online repository, with version and dependency management.
</p>

Some of the most popular NPM modules today are:

<li style="text-align: justify;">
  <a href="http://expressjs.com" target="_blank">express</a> &#8211; Express.js, a Sinatra-inspired web development framework for Node.js, and the de-facto standard for the majority of Node.js applications out there today.
</li>
<li style="text-align: justify;">
  <a href="http://www.senchalabs.org/connect/" target="_blank">connect</a> &#8211; Connect is an extensible HTTP server framework for Node.js, providing a collection of high performance “plugins” known as middleware; serves as a base foundation for Express.
</li>
<li style="text-align: justify;">
  <a href="http://socket.io" target="_blank">socket.io</a> and <a href="https://github.com/sockjs" target="_blank">sockjs</a> &#8211; Server-side component of the two most common websockets components out there today.
</li>
<li style="text-align: justify;">
  <a href="http://jade-lang.com" target="_blank">Jade</a> &#8211; One of the popular templating engines, inspired by HAML, a default in Express.js.
</li>
<li style="text-align: justify;">
  <a href="https://npmjs.org/package/mongodb" target="_blank">mongo</a> and <a href="https://github.com/gett/mongojs" target="_blank">mongojs</a> &#8211; MongoDB wrappers to provide the API for MongoDB object databases in Node.js.
</li>

NPM usage:

<pre class="lang:batch decode:true ">npm init                              - creates the package.json file and allow editing configuration
npm init -y                           - sets up a project with defaults, that is pretty useful for test projects or prototyping
npm search &lt;searchTerm&gt;               - search for package name
npm install &lt;packagename&gt;             - install package locally. npm install has a shorter alias which is npm i
npm install &lt;packagename&gt; --save      - install package locally and save it on package.json
npm install &lt;packagename&gt; --save-dev  - save dependencies into dev
npm start                             - starts the nodejs app based on package.json configuration
npm install -g &lt;packagename&gt;          - install package globally
npm update                            - updates local_modules packages
npm outdated                          - check for ourdate packages
npm uninstall &lt;packagename&gt; --save    - uninstall package</pre>

<p style="text-align: justify;">
  The best way to manage locally installed npm packages is to create a <strong>package.json</strong> file.
</p>

A **package.json** file allows you to:

  * documentat what packages your project depends on
  * it allows you to specify the versions of a package that your project can use using semantic versioning rules
  * makes your build reproducible which means that its way easier to share with other developers.

<p style="text-align: justify;">
  To specify the packages your project depends on, you need to list the packages you&#8217;d like to use in your <strong><code>package.json</code></strong> file. There are 2 types of packages you can list:
</p>

<li style="text-align: justify;">
  <code>"dependencies"</code>: these packages are required by your application in production
</li>
<li style="text-align: justify;">
  <code>"devDependencies"</code>: these packages are only needed for development and testing
</li>

<pre class="lang:js decode:true ">{
  "name": "my_package",
  "version": "1.0.0",
  "dependencies": {
    "my_dep": "^1.0.0"
  },
  "devDependencies" : {
    "my_test_framework": "^3.1.0"
  }
}</pre>

The above package.json specify that the app uses any version of the package `my_dep` that matches major version 1 in production, and requires any version of the package `my_test_framework` that matches major version 3, but only for development.

## <a href="http://www.iuliantabara.com/2015/12/extract-youtube-videosaudio/" target="_blank">2. Nodemon</a>

<p style="text-align: justify;">
  This is a tool to manage node processes during development. With <code>nodemon</code> you can start a node process and it keeps it running for. It utilizes <code>fsevents</code> to hook into filesystem changes and it restarts the node process on each file change.
</p>

<p style="text-align: justify;">
  You can install it using npm using the following command. I like to install it globally so I can use it for all projects, but you can remove the <code>-g</code> to install it locally instead.
</p>

<pre class="lang:batch decode:true ">npm install -g nodemon</pre>

<p style="text-align: justify;">
  Now instead of using <code>node server.js </code> to run your application, you can use <code>nodemon server.js</code>. It will watch for any changes in your application and automatically restart your server for you.
</p>

<h2 style="text-align: justify;">
  3. Node inspector
</h2>

<p style="text-align: justify;">
  <a href="https://github.com/node-inspector/node-inspector">Node Inspector</a> is a debugger interface for Node.js applications that uses the Blink Developer Tools. The really cool thing is that it works almost exactly as the Chrome Developer Tools.
</p>

<pre class="lang:batch decode:true ">npm install -g node-inspector</pre>

You should be a flavour of Chrome browser (Chrome, Chromium, etc) installed.

Once it is installed, you can run it using the following command. This will start the debugger and open your browser.

<pre class="lang:batch decode:true ">node-debug server.js</pre>

<p style="text-align: justify;">
  Can you combine nodemon with node inspector ? You would start your server with <code>nodemon --debug server.js</code> and then you&#8217;ll need to run node-inspector in a separate terminal window unless you push nodemon to the background.
</p>

<h2 style="text-align: justify;">
  4.  <a href="https://www.npmjs.com/package/helmet">Helmet</a>
</h2>

<p style="text-align: justify;">
  Can help protect your app from some well-known web vulnerabilities by setting HTTP headers appropriately.
</p>

<p style="text-align: justify;">
  Helmet is actually just a collection of nine smaller middleware functions that set security-related HTTP headers:
</p>

<li style="text-align: justify;">
  <a href="https://github.com/helmetjs/csp">csp</a> sets the <code>Content-Security-Policy</code> header to help prevent cross-site scripting attacks and other cross-site injections.
</li>
<li style="text-align: justify;">
  <a href="https://github.com/helmetjs/hide-powered-by">hidePoweredBy</a> removes the <code>X-Powered-By header</code>.
</li>
<li style="text-align: justify;">
  <a href="https://github.com/helmetjs/hpkp">hpkp</a> Adds <a href="https://developer.mozilla.org/en-US/docs/Web/Security/Public_Key_Pinning">Public Key Pinning</a> headers to prevent man-in-the-middle attacks with forged certificates.
</li>
<li style="text-align: justify;">
  <a href="https://github.com/helmetjs/hsts">hsts</a> sets <code>Strict-Transport-Security</code> header that enforces secure (HTTP over SSL/TLS) connections to the server.
</li>
<li style="text-align: justify;">
  <a href="https://github.com/helmetjs/ienoopen">ieNoOpen</a> sets <code>X-Download-Options</code> for IE8+.
</li>
<li style="text-align: justify;">
  <a href="https://github.com/helmetjs/nocache">noCache</a> sets <code>Cache-Control</code> and <code>Pragma</code> headers to disable client-side caching.
</li>
<li style="text-align: justify;">
  <a href="https://github.com/helmetjs/dont-sniff-mimetype">noSniff</a> sets <code>X-Content-Type-Options</code> to prevent browsers from MIME-sniffing a response away from the declared content-type.
</li>
<li style="text-align: justify;">
  <a href="https://github.com/helmetjs/frameguard">frameguard</a> sets the <code>X-Frame-Options</code> header to provide <a href="https://www.owasp.org/index.php/Clickjacking">clickjacking</a> protection.
</li>
<li style="text-align: justify;">
  <a href="https://github.com/helmetjs/x-xss-protection">xssFilter</a> sets <code>X-XSS-Protection</code> to enable the Cross-site scripting (XSS) filter in most recent web browsers.
</li>

## [5. Express-limiter][1]

<p style="text-align: justify;">
  Implement rate-limiting to prevent brute-force attacks against authentication.
</p>

<h2 style="text-align: justify;">
  6. <a href="https://www.npmjs.com/package/cluster-service" target="_blank">Cluster Service</a>
</h2>

<p style="text-align: justify;">
  <span class="repository-meta-content">Turn your single process code into a fault-resilient, multi-process service with built-in REST & CLI support. Restart or hot upgrade your web servers with zero downtime or impact to clients.</span>
</p>

 [1]: https://www.npmjs.com/package/express-limiter