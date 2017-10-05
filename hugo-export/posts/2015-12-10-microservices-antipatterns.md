---
title: Microservices antipatterns
author: Iulian
type: post
date: 2015-12-10T18:23:48+00:00
url: /2015/12/microservices-antipatterns/
categories:
  - Architecture

---
Takeaways from microservice antipatterns:

<li style="text-align: justify;">
  <strong>Overzeal­ous Services</strong> &#8211; don&#8217;t go straight into microservices, (fol­low­ing the hype per­haps?) rather than start­ing with the sim­plest thing possible. Instead, you should start by doing the sim­plest thing pos­si­ble, iden­tify the hotspots as they become appar­ent and then extract them into microservices. Start with monolith.
</li>
<li style="text-align: justify;">
  <strong>Schemas Every­where</strong> &#8211; avoid having a <strong>shared data­base</strong> between dif­fer­ent ser­vices. This cre­ates tight <strong>cou­pling</strong> between the ser­vices and means you can’t deploy the microservices inde­pen­dently if it breaks the shared schema. Any schema updates will force you to update other microservices too. Instead, every ser­vice should have its own data­base, and when you want data from another ser­vice you go through its <span class="caps">API</span> rather than reach­ing into its data­base and help yourself. Use <a href="http://semver.org/" target="_blank">semantic versioning</a>.
</li>
<li style="text-align: justify;">
  <strong>Spiky Load between Services</strong> &#8211; You often get spiky traf­fic between ser­vices, and a com­mon solu­tion is to amor­tise the load by using queues between the services. Use rabbitmq.com, redis, etc.
</li>
<li style="text-align: justify;">
  <strong>Hard­coded IPs and Ports</strong> &#8211; One solu­tion would be to use a <strong>dis­cov­ery ser­vice</strong> such as <a href="https://www.consul.io/" target="_blank">con­sul</a> or <a href="https://github.com/coreos/etcd" target="_blank">etcd</a>, and there’s also Netflix’s <a href="https://github.com/Netflix/eureka" target="_blank">eureka</a>. Another solu­tion is to use a <strong>cen­tralised router</strong>. Both solu­tions require reg­is­tra­tion and dereg­is­tra­tion, and both require high avail­abil­ity and scalability.
</li>
<li style="text-align: justify;">
  <strong>Dog­piles</strong> &#8211; If one of your ser­vices is under load or mal­func­tion­ing, and all your other ser­vices keep retry­ing their failed calls, then the prob­lem would be com­pounded and mag­ni­fied by the addi­tional load from these retries. The solu­tion here is to have expo­nen­tial back­off and imple­ment the <a href="http://martinfowler.com/bliki/CircuitBreaker.html" target="_blank">cir­cuit breaker</a> pattern. Many libraries such as Netflix’s <a href="https://github.com/Netflix/Hystrix" target="_blank">Hys­trix</a> and <a href="https://github.com/michael-wolfenden/Polly" target="_blank">Polly</a> for .Net are available. You can even use the ser­vice dis­cov­ery layer to help prop­a­gate the mes­sage to all your ser­vices that a cir­cuit has been tripped (i.e. a ser­vice is struggling).
</li>
<li style="text-align: justify;">
  <strong>Debug­ging Hell</strong> &#8211; Debug­ging is always a huge issue in micro-service archi­tec­tures. E.g., a nested ser­vice call fails and you can’t cor­re­late it back to a request com­ing from the user. One solu­tion is to use <strong>cor­re­la­tion IDs</strong>. When a request comes in, you assign the request with a cor­re­la­tion <span class="caps">ID</span> and pass it on to other ser­vices in the <span class="caps">HTTP</span> request header. Every ser­vice would do the same and pass the cor­re­la­tion <span class="caps">ID</span> it receives in the incom­ing <span class="caps">HTTP</span> header in any out-going requests. When­ever you log a mes­sage, be sure to include this cor­re­la­tion <span class="caps">ID</span> in the log line.
</li>
<li style="text-align: justify;">
  <strong>Miss­ing Mock Servers</strong> &#8211; When you have a ser­vice that other teams depend on, each of these teams would have to mock and stub your ser­vice in order to test their own services. A good step in the right direc­tion is for you to own a mock ser­vice and pro­vide it to con­sumers.  You can take it a step fur­ther, by build­ing the mock ser­vice into the client so you don’t even have to main­tain a mock ser­vice any­more. Use <a href="https://anypoint.mulesoft.com/apiplatform/" target="_blank">Any­point Plat­form</a> or <a href="http://swagger.io/" target="_blank">Swagger</a>.
</li>
<li style="text-align: justify;">
  Fly­ing Blind &#8211; There are more than a hand­ful of tools avail­able in this space. There are com­mer­cial tools (some with free tiers) such as <a href="http://newrelic.com/" target="_blank">NewRelic</a> and <a href="http://www.stackdriver.com/" target="_blank">Stack­Driver</a> (now inte­grated into Google AppEn­gin), <span class="caps">AWS</span> also offers <a href="http://aws.amazon.com/cloudwatch/" target="_blank">Cloud­Watch</a> as part of its ecosys­tem. In the open source space, Net­flix has been lead­ing the way with <a href="https://github.com/Netflix/Hystrix/wiki/Dashboard" target="_blank">Hys­trix</a> and  some­thing <a href="http://techblog.netflix.com/2015/02/a-microscope-on-microservices.html" target="_blank">even more excit­ing</a>.
</li>

### Links:

  * <a href="http://highscalability.com/blog/2014/4/8/microservices-not-a-free-lunch.html" target="_blank">http://highscalability.com/blog/2014/4/8/microservices-not-a-free-lunch.html</a>
  * <a href="http://mcfunley.com/choose-boring-technology" target="_blank">http://mcfunley.com/choose-boring-technology</a>
  *  <a href="https://www.youtube.com/watch?v=SQYCzAWlrHU" target="_blank">Richard Rodger &#8211; Measuring Micro-services &#8211; Codemotion Rome 2015</a>
  * <a href="http://www.slideshare.net/InfoQ/netflix-built-its-own-monitoring-system-and-why-you-probably-shouldnt" target="_blank">http://www.slideshare.net/InfoQ/netflix-built-its-own-monitoring-system-and-why-you-probably-shouldnt</a>

&nbsp;