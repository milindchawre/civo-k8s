[Fairwinds Goldilocks](https://github.com/FairwindsOps/goldilocks) is an open source tool that recommends the right settings for [resource requests and limits](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits) on kubernetes workloads. It was built by [Fairwinds (formerly ReactiveOps)](https://www.fairwinds.com/).

#### Problem
It is said that good fences make good neighbors, it's true for applications too. Applications were typically designed to run standalone in a machine and use all of the resources at hand. But that's not the case with distributed system (like kubernetes) where resources (CPU/Memory, etc) are shared between applications. Every time you create a workload in Kubernetes, its very important to specify how much memory and CPU that workload is going to use. There are two ways to specify the resources and both have some drawbacks:
- `set the resource requests too low`, which has the risk of making a normal application looks like it’s using too much memory, too much CPU and kubernetes will kill it. Needless to say, this will make your application unreliable and cause downtime.
- `set the resource requests too high`, where you allocate more compute than necessary and your cloud bill can get to be much higher than it needs to.

So setting optimal values for [resource requests and limits](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits) on kubernetes workloads is very important. This is where Fairwind's Goldilocks helps - it will suggest resource request values that are just right.

#### Goldilocks Features
- Recommends values for resource requests and limits on kubernetes workloads.
- Dashboard that shows recommended values for kubernetes resources based on QoS(Quality of Service).
- Helps in optimizing kubernetes resource utilization.
- CLI utility for analysis of kubernetes deployment resource usage.

#### Installation
The goldilocks can be [installed](https://github.com/FairwindsOps/goldilocks#installation) using `kubectl`, `helm` or a local binary.

All methods will require you to have a cluster running, and the `KUBECONFIG` environment variable set up.

- `kubectl` to be able to manually install Goldilocks, you would have to [clone the repository](https://github.com/FairwindsOps/goldilocks) and from its root directory run the following:
```
kubectl create namespace goldilocks
kubectl -n goldilocks apply -f hack/manifests/controller
kubectl -n goldilocks apply -f hack/manifests/dashboard
```
- helm
```
helm repo add fairwinds-stable https://charts.fairwinds.com/stable
helm install --name goldilocks --namespace goldilocks --set installVPA=true fairwinds-stable/goldilocks
```
- binary : download the binary from [release page](https://github.com/FairwindsOps/goldilocks/releases)

then use kubectl's port-forward function to access the dashboard:
```
kubectl -n goldilocks port-forward svc/goldilocks-dashboard 8080:80
```
and visit `http://localhost:8080/` to view the dashboard.

![goldilocks1](https://github.com/milindchawre/civo-k8s/raw/master/blog/goldilocks/images/g1.png)

As shown in the dashboard, there are no initial recommendations for any deployment. This is because Goldilocks only provides 
recommendations for deployments in those namespace which have the label `goldilocks.fairwinds.com/enabled=true` set.

Let's assign this label to our Goldilocks namespace: `kubectl label ns goldilocks goldilocks.fairwinds.com/enabled=true`.

Now you should see some recommendations for every deployments in the goldilocks namespace.

![goldilocks2](https://github.com/milindchawre/civo-k8s/raw/master/blog/goldilocks/images/g2.png)

As shown in the image above, Goldilocks provides two types of recommendations, depending on what [QoS Class](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) you desire for your deployments.

#### How goldilocks provides resource recommendation?

Behind the scenes, goldilocks makes use of [Vertical Pod Autoscaler (VPA)](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler), which contains recommendation engine that takes into account the current resource usage of your deployments in order to provide a guideline.

The primary goal of VPA is to actually set those for you, but the company isn’t comfortable with the way it does this and 
prefers to use it primarily for recommendations, Goldilocks’s creator [Andy Suderman](https://www.linkedin.com/in/sudermanjr/) writes in a [blog post](https://www.fairwinds.com/news/introducing-goldilocks-a-tool-for-recommending-resource-requests).

>*"The way we utilize the VPA recommendation engine is simple. We run a controller in the cluster that watches for namespaces that are labelled with `goldilocks.fairwinds.com/enabled=true`. Within those enabled namespaces, we look at every deployment object and create a corresponding VPA object. That VPA is set with `Mode: Off ` and doesn’t actually modify your resource requests and limits; it just sits there and provides a recommendation. To view these recommendations, you would have to use kubectl to query each and every VPA object. For medium to large deployments, this can get very tedious. That’s where we introduced Goldilocks dashboard which provides a visualization of the VPA recommendations."*

#### Excluding Recommendations for Containers
Often we see clusters where every pod gets a sidecar, such as the Istio or Linkerd2 sidecars. You may not want to see resource recommendations for those containers because we may or may not have control over those values. There are two ways to exclude these containers.

The first method is global and can be set by running the dashboard with the flag `--exclude-containers "istio-proxy,linkerd-proxy"`. This is set by default in the Helm chart installation.

The second method is to exclude containers per deployment using a label on the deployment itself: `goldilocks.fairwinds.com/exclude-containers=linkerd-proxy,istio-proxy`.

If you are running any other containers you wish to exclude, you can just substitute them in for the values in the examples above, or add to the list.

#### Goldilocks CLI
If you don't want to deploy Goldilocks in your Kubernetes cluster as an application running along with other workloads, you can make use of [Goldilocks CLI](https://github.com/FairwindsOps/goldilocks#cli-usage-not-recommended). You can do everything described above with the CLI tool, but do note that Fairwinds primarily recommends the use of the Dashboard.

For more information, check out the [Goldilocks project on GitHub](https://github.com/FairwindsOps/goldilocks) and this introduction [video](https://www.youtube.com/watch?v=WwiRDJ9THMc) for Goldilocks.
