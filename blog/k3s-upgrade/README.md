
![k3s-header-image](https://github.com/milindchawre/civo-k8s/raw/master/blog/k3s-upgrade/images/k3s-header-image.png)

### Introduction

[K3s](https://k3s.io/) is a lightweight Kubernetes distribution which runs with a very small footprint and is a [suitable candidate](https://rancher.com/blog/2019/why-k3s-is-the-future-of-k8s-at-the-edge/) for [edge computing](https://en.wikipedia.org/wiki/Edge_computing). K3s is capable of nearly everything k8s can do. It is just a more lightweight version. It is the ONLY certified Kubernetes distribution built for IoT & Edge computing. It is a highly available, certified Kubernetes distribution designed for production workloads in unattended, resource-constrained, remote locations or inside IoT appliances. It is packaged as a single <40MB binary that reduces the dependencies and steps needed to install, run and auto-update a production Kubernetes cluster. K3s is optimized for ARM. Both ARM64 and ARMv7 are supported with binaries and multi-arch images available for both. K3s works great from something as small as a Raspberry Pi to an AWS a1.4xlarge 32GiB server.

If you are running some old version of [k3s](https://k3s.io/) and want to upgrade to some new stable version, then this is the guide for you. In this blog, we are going to discuss the [automated way](https://rancher.com/docs/k3s/latest/en/upgrades/automated/) for k3s upgrade compared to the [manual way](https://rancher.com/docs/k3s/latest/en/upgrades/basic/).

![k3s-upgrade](https://github.com/milindchawre/civo-k8s/raw/master/blog/k3s-upgrade/images/k3s-upgrade.png)

### Steps for k3s upgradation

##### _Pre-requisite_

- k3s cluster (which you want to upgrade)
- kubectl (to communicate with k3s cluster)
- KUBECONFIG env pointing to your kubernetes config file

##### _Connect to the cluster_

First connect to your k3s cluster and list down all the k8s nodes.
```
# Ensure KUBECONFIG env is pointing to your kubernetes config file
$ kubtctl get nodes
NAME               STATUS   ROLES    AGE    VERSION
kube-node-c982     Ready    <none>   130m   v1.16.3-k3s1
kube-master-ce86   Ready    master   131m   v1.16.3-k3s1
kube-node-4d85     Ready    <none>   130m   v1.16.3-k3s1
```
As you can see all the nodes are running `v1.16.3-k3s1` of k3s.

##### _Install the system-upgrade-controller_

The k3s cluster upgradation is handled by a controller named [system-upgrade-controller](https://github.com/rancher/system-upgrade-controller) which leverages a [custom resource definition (CRD)](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#custom-resources), the `plan`, and a [controller](https://kubernetes.io/docs/concepts/architecture/controller/) that schedules upgrades based on the configured plans. This `system-upgrade-controller` need to be installed as a deployment in your k3s cluster. To install it run following command:
```
kubectl apply -f https://raw.githubusercontent.com/rancher/system-upgrade-controller/master/manifests/system-upgrade-controller.yaml
```
You should see the upgrade controller starting in `system-upgrade` namespace as shown below.
```
$ kubectl get all -n system-upgrade
NAME                                             READY   STATUS    RESTARTS   AGE
pod/system-upgrade-controller-7fff98589f-8cjsv   1/1     Running   0          23s

NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/system-upgrade-controller   1/1     1            1           24s

NAME                                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/system-upgrade-controller-7fff98589f   1         1         1       24s
```

##### _Configure Plans_

The Plan defines upgrade policies and requirements. The controller schedules upgrades by monitoring these plans and selecting nodes to run upgrade [jobs](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/) on it. So lets create two plans: one for server (master) nodes and other one for agent (worker) nodes. The following two example plans will upgrade your cluster to k3s `v1.18.4+k3s1`.
```
# Server plan
apiVersion: upgrade.cattle.io/v1
kind: Plan
metadata:
  name: server-plan
  namespace: system-upgrade
spec:
  concurrency: 1
  cordon: true
  nodeSelector:
    matchExpressions:
    - key: k3s-master-upgrade
      operator: In
      values:
      - "true"
  serviceAccountName: system-upgrade
  upgrade:
    image: rancher/k3s-upgrade
  version: v1.18.4+k3s1
---
# Agent plan
apiVersion: upgrade.cattle.io/v1
kind: Plan
metadata:
  name: agent-plan
  namespace: system-upgrade
spec:
  concurrency: 1
  cordon: true
  nodeSelector:
    matchExpressions:
    - key: k3s-worker-upgrade
      operator: In
      values:
      - "true"
  prepare:
    args:
    - prepare
    - server-plan
    image: rancher/k3s-upgrade
  serviceAccountName: system-upgrade
  upgrade:
    image: rancher/k3s-upgrade
  version: v1.18.4+k3s1
```
There are few important fields to notice in above plans.
- There are two plans: `server-plan and agent-plan` to define upgrade policies for both types of nodes in k3s.
- The `concurrency` field: that defines how many nodes can be upgraded at the same time.
- The `nodeSelector` field is used to specify nodes to which a particular plan will be applied. In our case nodes with label `k3s-master-upgrade=true` targets master nodes while label `k3s-worker-upgrade=true` targets worker nodes of k3s.
- The `prepare` field in the agent-plan will cause upgrade jobs for that plan to wait for the server-plan to complete before they execute. So as to ensure that all master nodes are upgraded prior to the worker nodes.
- The `image: rancher/k3s-upgrade` field specify the [k3s-upgrade](https://github.com/rancher/k3s-upgrade) image which is responsible of upgrading k3s version via the [System Upgrade Controller](https://github.com/rancher/system-upgrade-controller), it does so by replacing the k3s binary with the new version and killing the old k3s process allowing the supervisor to restart k3s with the new version.
- The `version` field specify the version of k3s to which we need to upgrade. In our case, it is set to version `v1.18.4+k3s1`. Alternatively, you can skip the version field altogether by using another field `channel: https://update.k3s.io/v1-release/channels/stable` using which we can ask plan to pull latest stable version of k3s.

##### _Assign labels to nodes_

Label the nodes that you want to upgrade with the right label:
```
# For master nodes
kubectl label node <node-name> k3s-master-upgrade=true
                   OR
# For worker nodes
kubectl label node <node-name> k3s-worker-upgrade=true
```
In our case its:
```
$ kubectl label node kube-master-ce86 k3s-master-upgrade=true
node/kube-master-ce86 labeled
$ kubectl label node kube-node-c982  k3s-worker-upgrade=true
node/kube-node-c982 labeled
$ kubectl label node kube-node-4d85 k3s-worker-upgrade=true
node/kube-node-4d85 labeled
```

##### _Apply plans_

Now apply the plans defined above using kubectl apply command. 
```
$ kubectl apply -f plans.yaml
plan.upgrade.cattle.io/server-plan created
plan.upgrade.cattle.io/agent-plan created
$ kubectl get plans -n system-upgrade
NAME          AGE
server-plan   14s
agent-plan    14s
```
As soon as you apply the plan, the controller will detect it and will begin the upgrade process. On the other hand, if you update any existing plans the controller will re-evaluate the plan and will determine if another upgrade is needed.

##### _Verify the upgrade_

You can monitor the progress of an upgrade by viewing the plan and jobs via kubectl:
```
kubectl -n system-upgrade get plans -o yaml
kubectl -n system-upgrade get jobs -o yaml
```
Once the upgrade is done. Verify the new installed version on all the nodes using kubectl:
```
$ kubectl get nodes
NAME               STATUS   ROLES    AGE    VERSION
kube-master-ce86   Ready    master   147m   v1.18.4+k3s1
kube-node-c982     Ready    <none>   147m   v1.18.4+k3s1
kube-node-4d85     Ready    <none>   147m   v1.18.4+k3s1
```

##### _Conclusion_

The [k3s-upgrade](https://github.com/rancher/k3s-upgrade) and [system-upgrade-controller](https://github.com/rancher/system-upgrade-controller) makes the overall k3s upgrade process easier compared to the [manual upgrade method](https://rancher.com/docs/k3s/latest/en/upgrades/basic/) with a minimal downtime as the k3s upgradation proceeds in rolling deployment fashion.
