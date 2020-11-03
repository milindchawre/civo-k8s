#### Introducing Sloop

![sloop-intro](https://github.com/milindchawre/civo-k8s/raw/master/blog/sloop/images/sloop-intro.png)

[Sloop](https://github.com/salesforce/sloop) is an open source tool to visualize history of all resources in your kubernetes cluster. It was built by [salesforce](https://www.salesforce.com).

#### Problem
Workloads running in Kubernetes cluster are dynamic in nature. The pods, replicas, deployments in your cluster keeps going on and off over the period of time due to their ephemeral nature. There are lot of situations when you want to check what happened in your cluster:
* to debug historic incidents
* to debug common tasks like: 
  - finding info and events related to kubernetes resources (like pods, replicasets, deployments, etc) that have been deleted like:
    * finding info related to pods/replicasets those are replaced by newer pods/replicasets after deployment update.
    * getting details of pods evicted from lost node.
    * getting info about lost kubernetes nodes, which no longer exists.
    * knowing rollout details of older deployments.
    * discover host where pods from previous deployment were running.
    * retrieving timings of pod replacements and their health checks.
    * long term behavioral analysis of your workloads running on your kubernetes cluster
    * basically we need info about all the events happening in kubernetes cluster.
    * and so on...

#### Kubernetes Events
[Kubernetes events](https://kubernetes.io/docs/tasks/debug-application-cluster/events-stackdriver/) is the answer to tackle above problem. Kubernetes events are great way to analyze past events in your cluster, since it captures all the events and resource state changes happening in your cluster. But there are few drawbacks.
- Kubernetes Events can be accessed using only kubectl
- The default retention period of kubernetes events is [1hr](https://github.com/kubernetes/kubernetes/blob/a86afc12df023e8a45a1c3b6523aed38c3e6cd18/cmd/kube-apiserver/app/options/options.go#L108).
- The retention period can be increased using `--event-ttl` flag of kube-apiserver. But doing so [can cause some serious problems](https://github.com/kubernetes/kubernetes/issues/52521#issuecomment-329803408) with etcd cluster. 
- There is no anyway to visualize these events.

To address few of the problems mentioned above tools like [eventer](https://github.com/kubernetes-retired/heapster/tree/master/events), [eventrouter](https://github.com/heptiolabs/eventrouter) and [event-exporter](https://github.com/caicloud/event_exporter) where developed.

[Eventer](https://github.com/kubernetes-retired/heapster/tree/master/events) - Unfortunately eventer tool is now deprecated.

[Eventrouter](https://github.com/heptiolabs/eventrouter) - In eventrouter, the kubernetes events are captured and routed to a backend sink. A sink can be anything like an Amazon S3 bucket or an elasticsearch cluster where you can dump all your events, later you can create dashboards based on captured events using tools like [kibana](https://www.elastic.co/kibana)/[grafana](https://grafana.com/). It supports multiple sinks.

 [Event-exporter](https://github.com/caicloud/event_exporter) - It is prometheus exporter to expose kubernetes events in prometheus format, which can then be stored in your prometheus server and you can then either create alerts using [alertmanager](https://prometheus.io/docs/alerting/latest/alertmanager/) or create dashboard using [grafana](https://grafana.com/) based on these collected events.

Above mentioned tools are good way to tackle most of the challenges faced by kubernetes events. But these are not a standalone solution, you have lot of work to do as an end user. You also need to configure other tools apart from these one to store and visualize the events.

#### Sloop: Your Ultimate and Easy Solution
[Sloop](https://github.com/salesforce/sloop) is a standalone solution which can store and visualize kubernetes events without needing much efforts from an end user perspective. Sloop monitors Kubernetes, recording histories of events and resource state changes, providing visualizations to aid in debugging past events. 

#### Key Features
- Allows you to find and inspect resources that no longer exist in your kubernetes cluster.
- Helps in answering almost all the queries mentioned at the beginning of this blog.
- Provides a timeline displays that shows rollouts of related resources in updates to Deployments, ReplicaSets and StatefulSets.
- Helps in debugging transient and intermittent errors.
- Allows you to see changes over time in a Kubernetes application.
- Is a self-contained service with no dependencies on distributed storage.

#### Installation
[Sloop](https://github.com/salesforce/sloop) can be installed using `helm` or as a standalone `docker container`.

All methods will require you to have a kubernetes cluster running, and the `KUBECONFIG` environment variable set up. If you have not yet signed up to Civo, you can [sign up to apply](https://www.civo.com/signup) for our managed Kubernetes beta to try this out for yourself! During the beta, all users will get $70 worth of credit on their month-end statements, which is enough to run a three-node cluster 24/7 for the month.

- Helm
```
$ git clone https://github.com/salesforce/sloop
$ cd sloop/helm
$ kubectl create ns sloop
$ helm install sloop -n sloop ./sloop
```

- Docker container
Refer [this](https://github.com/salesforce/sloop#local-docker-run) to run sloop as a standalone docker container.

then use kubectl's port-forward function to access the dashboard:
```
kubectl port-forward -n sloop service/sloop 8080:80
```
and visit `http://localhost:8080/` to view the dashboard.

![sloop1](https://github.com/milindchawre/civo-k8s/raw/master/blog/sloop/images/sloop1.png)

As you can see sloop provides timeline of your kubernetes resources. It also provides different filters to visualize it.

![sloop2](https://github.com/milindchawre/civo-k8s/raw/master/blog/sloop/images/sloop2.png)

Sloop also expose debug menu where you can see it's config, internal metrics and different settings. You can also query its internal data store, there are lot of things to tweak around here.

![sloop-in-action](https://github.com/milindchawre/civo-k8s/raw/master/blog/sloop/images/sloop.gif)

As you can see you can filter out kubernetes resources based on the time range, the kubernetes namespace, the kind of kubernetes resource (like pods, pvc, node, etc), the kubernetes resource name and also sort it out based on different options. Selecting a particular kubernetes resource in a specified timeline will show you different events occurring at that particular moment on that resource. This helps in capturing all past events those happened on that resources in your cluster.

#### Conclusion
For more information, check out the [Sloop project on GitHub](https://github.com/salesforce/sloop).

If you found this guide useful, let us know on Twitter at [@civocloud](https://twitter.com/civocloud)! You can also reach me out on Twitter at [@milindchawre](https://twitter.com/milindchawre).

