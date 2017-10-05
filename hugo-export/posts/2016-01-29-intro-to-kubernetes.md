---
title: 'Private: Intro to Kubernetes'
author: Iulian
type: post
date: 2016-01-29T19:41:00+00:00
draft: true
private: true
url: /2016/01/intro-to-kubernetes/
categories:
  - DevOps
  - Kubernetes
  - ReactJS
tags:
  - Docker
  - Kubernetes

---
<p style="text-align: justify;">
  Docker is the solution to decouple your application from host environment (OS) and pack it into a container and adhere to the &#8220;build once, run anywhere&#8221; philosophy. But shortly we&#8217;ll realize we&#8217;ll need more than just packing and isolation.
</p>

<p style="text-align: justify;">
  A couple of questions will look for solutions:
</p>

<p style="text-align: justify;">
  <strong>Scheduling</strong> &#8211; Where should my containers run ?
</p>

<p style="text-align: justify;">
  <strong>Life-cycle and health</strong> &#8211; I want my containers running despite of failures.
</p>

<p style="text-align: justify;">
  <strong>Discovery &#8211;</strong> Where are my containers now ?
</p>

<p style="text-align: justify;">
  <strong>Monitoring</strong> &#8211; What&#8217;s happening with my containers ?
</p>

<p style="text-align: justify;">
  <strong>Auth {x, y}</strong> &#8211; Control who can do things in my containers.
</p>

<p style="text-align: justify;">
  <strong>Aggregates</strong> &#8211; Compose set of containers into jobs.
</p>

<p style="text-align: justify;">
  <strong>Scaling</strong> &#8211; Makes jobs bigger or smaller.
</p>

<p style="text-align: justify;">
  <a href="http://kubernetes.io/" target="_blank">Kubernetes </a>is a system for managing containerized applications across a cluster of nodes, providing basic mechanisms for deployment, maintenance, and scaling of applications.
</p>

<p style="text-align: justify;">
  The name <strong>Kubernetes</strong> originates from Greek, meaning &#8220;helmsman&#8221; or &#8220;pilot&#8221;, and is the root of &#8220;governor&#8221; and <a href="http://www.etymonline.com/index.php?term=cybernetics">&#8220;cybernetic&#8221;</a>. <strong>K8s</strong> is an abbreviation derived by replacing the 8 letters &#8220;ubernete&#8221; with 8.
</p>

<h2 style="text-align: justify;">
  Primary concepts:
</h2>

  * **Container** &#8211; a sealed application package (Docker image)
  * **Pod** &#8211; a group of Containers
  * **Labels** &#8211; Identifying metadata attached to objects, queryable by selectors
  * **Kubelet** &#8211; Container Agent
  * **Proxy** &#8211; A load balancer for Pods
  * **etcd** &#8211; A metadata service
  * **Replication Controller** &#8211; Manages replication of pod, driving current state towards desired state
  * **Scheduler** &#8211; Schedules pods in worker nodes
  * **API Server** &#8211; Kubernetes API server

<h2 style="text-align: justify;">
  Kubernetes architecture
</h2>

A Kubernetes cluster is comprised of a master and N nodes. The master acts as a control plane for the cluster.

## <a href="https://www.iuliantabara.com/2016/01/intro-to-kubernetes/kubernetes_architecture/" rel="attachment wp-att-1033"><img class="aligncenter size-full wp-image-1033" src="https://www.iuliantabara.com/wp-content/uploads/2016/01/kubernetes_architecture.png" alt="kubernetes_architecture.png" width="935" height="394" srcset="https://www.iuliantabara.com/wp-content/uploads/2016/01/kubernetes_architecture.png 935w, https://www.iuliantabara.com/wp-content/uploads/2016/01/kubernetes_architecture-300x126.png 300w, https://www.iuliantabara.com/wp-content/uploads/2016/01/kubernetes_architecture-768x324.png 768w, https://www.iuliantabara.com/wp-content/uploads/2016/01/kubernetes_architecture-500x211.png 500w" sizes="(max-width: 935px) 100vw, 935px" /></a>Components

<p style="text-align: justify;">
  <strong>1.1. Master server</strong> &#8211; The controlling unit in a Kubernetes cluster is called the<span class="Apple-converted-space"> </span>master<span class="Apple-converted-space"> </span>server. It serves as the main management contact point for administrators and it also provides many cluster-wide systems for the relatively dumb worker nodes. The master server runs a number of unique services that are used to manage the cluster&#8217;s workload and direct communications across the system.
</p>

<p style="text-align: justify;">
  Kubernetes uses<span class="Apple-converted-space"> </span><code>etcd</code><span class="Apple-converted-space"> </span>to store configuration data that can be used by each of the nodes in the cluster. This can be used for service discovery and represents the state of the cluster that each component can reference to configure or reconfigure themselves.
</p>

<p style="text-align: justify;">
  <strong>1.2. Node</strong> &#8211; A node provides the runtime environments for containers. Each node in a Kubernetes cluster has the required services to be managed by the <a href="https://docs.openshift.org/latest/architecture/infrastructure_components/kubernetes_infrastructure.html#master">master</a>. Nodes also have the required services to run pods, including Docker, a <a href="https://docs.openshift.org/latest/architecture/infrastructure_components/kubernetes_infrastructure.html#kubelet">kubelet</a>, and a <a href="https://docs.openshift.org/latest/architecture/infrastructure_components/kubernetes_infrastructure.html#service-proxy">service proxy</a>.
</p>

<p style="text-align: justify;">
  <strong>1.3. Pod</strong> &#8211; In Kubernetes, all containers run inside pods. A pod can host a single container or multiple cooperating containers. The containers in the pod are guaranteed to be co-located on the same machine and can share resources. The pod can contain volumes which are directories that are private to a container or shared across containers in a pod.
</p>

<p style="text-align: justify;">
  For each pod the user creates, Kubernetes will find a node that is healthy and has the available capacity to host the pod.
</p>

## Pod lifecycle {#etcd}

<p style="text-align: justify;">
  Once attached to a node, pods do not automatically move/migrate even we restart the pod. In order to move/restart a pod the user should define a replication controller.
</p>

<p style="text-align: justify;">
  <strong>1.4. Replication controller</strong> &#8211; ensures that a specified number of pods &#8220;replicas&#8221; are running at any time (RestartPolicy = Always). Replication controller is using pod templates to create pods and pod labels to monitor and maintain the number of pods to the desired level. Once the pods are created, the system continually monitors their health and that of the machines they are running on; if a pod fails due to a software problem or machine failure, the replication controller automatically creates a new pod on a healthy machine, to maintain the set of pods at the desired replication level.
</p>

<p style="text-align: justify;">
  <a href="https://www.iuliantabara.com/2016/01/intro-to-kubernetes/replication_controller/" rel="attachment wp-att-1034"><img class="aligncenter size-full wp-image-1034" src="https://www.iuliantabara.com/wp-content/uploads/2016/01/replication_controller.png" alt="Replication Controller" width="541" height="383" srcset="https://www.iuliantabara.com/wp-content/uploads/2016/01/replication_controller.png 541w, https://www.iuliantabara.com/wp-content/uploads/2016/01/replication_controller-300x212.png 300w, https://www.iuliantabara.com/wp-content/uploads/2016/01/replication_controller-424x300.png 424w" sizes="(max-width: 541px) 100vw, 541px" /></a>
</p>

<p style="text-align: justify;">
  <strong>1.5.Services</strong> &#8211; they define a TCP/UDP port reservation. Provides a way for applications running in containers to connect to each other without requiring that each one to be configured with the endpoint IP Address. A service maps the ports of the containers running on pods across multiple nodes to externally accessible ports.
</p>

<p style="text-align: justify;">
  <a href="http://www.iuliantabara.com/2016/01/intro-to-kubernetes/node-service/" rel="attachment wp-att-1007"><img class="aligncenter size-full wp-image-1007" src="http://www.iuliantabara.com/wp-content/uploads/2016/01/node-service.png" alt="node service" width="620" height="430" srcset="https://www.iuliantabara.com/wp-content/uploads/2016/01/node-service.png 620w, https://www.iuliantabara.com/wp-content/uploads/2016/01/node-service-300x208.png 300w, https://www.iuliantabara.com/wp-content/uploads/2016/01/node-service-433x300.png 433w" sizes="(max-width: 620px) 100vw, 620px" /></a><strong>1.6.Kubelet</strong> &#8211; it&#8217;s the Kubernetes note agent and will take care of each container state: if a container fails it can be automatically restarted by Kubelet.
</p>

<p style="text-align: justify;">
  <a href="http://www.iuliantabara.com/2016/01/intro-to-kubernetes/kubelet/" rel="attachment wp-att-1009"><img class="aligncenter size-full wp-image-1009" src="http://www.iuliantabara.com/wp-content/uploads/2016/01/kubelet.png" alt="kubelet" width="614" height="446" srcset="https://www.iuliantabara.com/wp-content/uploads/2016/01/kubelet.png 614w, https://www.iuliantabara.com/wp-content/uploads/2016/01/kubelet-300x218.png 300w, https://www.iuliantabara.com/wp-content/uploads/2016/01/kubelet-413x300.png 413w" sizes="(max-width: 614px) 100vw, 614px" /></a><strong>Kubecfg</strong> &#8211; The command line client that connects to the master to administer Kubernetes.
</p>

<p style="text-align: justify;">
  References:
</p>

<li style="text-align: justify;">
  <a href="http://kubernetes.io/v1.1/docs/design/README.html" target="_blank">http://kubernetes.io/v1.1/docs/design/README.html</a>
</li>