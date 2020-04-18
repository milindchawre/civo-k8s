# civo-k8s
Tutorials on Civo Cloud K8s offering

# Menu

- [Civo Cloud Kube100 Program](#civo-cloud-kube100-program)
- [Enrollment Process](#enrollment-process)
- [Creating your first kubernetes cluster](#creating-your-first-kubernetes-cluster)
    - [Using Web UI](#using-web-ui)
    - [Using command line](#using-command-line)

## Civo Cloud Kube100 Program
[Civo-Cloud](https://www.civo.com/) is building the world's first managed Kubernetes service powered by k3s. This service is in beta phase and they have introduced [kube100](https://www.civo.com/kube100) program where they offer free credits to test out this service.

## Enrollment Process
- Apply for kube100 program [here](https://www.civo.com/kube100), where they go through your profile and decide whether to enroll you.
- Once enrolled they will send you an invite to join there [community slack channel](https://civo-community.slack.com/) where you can ask questions and give feedback.
- You can't singup to the slack channel, but you can join only if you got the invitation after enrolling in kube100 program.

## Creating your first kubernetes cluster
- Once you got the invitation to join the slack channel and access to the kube100 program, go ahead and join the slack community; sign in to your civo cloud account to create your first k8s cluster.
- There are two ways to create your k8s cluster.
  1. [Using Web UI](#using-web-ui)
  2. [Using command line](#using-command-line)

## Using Web UI
- This is what you see after sign in.

![cc1](https://github.com/milindchawre/civo-k8s/raw/master/images/cc1.png)


- Click on create cluster and fill-in the details like the cluster name, no. of k8s nodes and the size of machines as shown below.

![cc2](https://github.com/milindchawre/civo-k8s/raw/master/images/cc2.png)


- Now select the applications you want to be pre-configured inside your k8s cluster ans click create as shown below.

![cc3](https://github.com/milindchawre/civo-k8s/raw/master/images/cc3.png)

- Your cluster should be ready now, download the kubeconfig file and play with it.

![cc4](https://github.com/milindchawre/civo-k8s/raw/master/images/cc4.png)

## Using command line
- You can interact with civo cloud through command line tool.
- To install the `civo cli` follow below steps:
```
- Install ruby
- Install civo-cli : gem install civo_cli
```
- Verify civo is installed
```
$ civo version
You are running the current v0.5.9 of Civo CLI
```
- To interact with civo cloud you need apikey. The key can be retrieved by following [these](https://www.civo.com/api) steps.
- Add the apikey
```
$ civo apikey add demokey Sd82njaowcyskmof873h63mow7n4n2saf20fnscvaro3fqp0c7
Saved the API Key Sd82njaowcyskmof873h63mow7n4n2saf20fnscvaro3fqp0c7 as demokey
The current API Key is now demokey
$
$ civo apikey list
+---------+----------------------------------------------------+----------+
| Name    | Key                                                | Default? |
+---------+----------------------------------------------------+----------+
| demokey | Sd82njaowcyskmof873h63mow7n4n2saf20fnscvaro3fqp0c7 | <=====   |
+---------+----------------------------------------------------+----------+
```
- To create kubernetes cluster use `civo kubernetes create` command.
```
$ civo kubernetes help create
Usage:
  civo kubernetes create [NAME] [...]

Options:
              [--size=size]
                                                                   # Default: g2.medium
              [--nodes=node_count]
                                                                   # Default: 3
              [--version=version]
              [--wait=wait until cluster is running], [--no-wait]
              [--save], [--no-save]
              [--switch], [--no-switch]
  apps, app, application, [--applications=APPLICATIONS]
              [--remove-applications=REMOVE_APPLICATIONS]

Description:
  Create a new Kubernetes cluster with name (randomly assigned if blank), instance size (default: g2.medium),

  Optional parameters are as follows:
   --size=<instance_size> - 'g2.medium' if blank. List of sizes and codes to use can be found through `civo sizes`
   --nodes=<count> - '3' if blank
   --version=<version> - our latest k3s version if blank
   --applications=name1,name2 - optional, use names shown by running `civo applications`
   --remove-applications=name1,name2 - optional, remove default application names shown by running `civo applications`
   --wait - wait for build to complete and show status. Off by default.
   --save - save resulting configuration to ~/.kube/config (requires kubectl and the --wait option)
   --switch - switch context to newly-created cluster (requires kubectl and the --wait and --save options, as well as existing kubeconfig file)
```
- The create command is self-explanatory.
```
$ civo kubernetes create --size=g2.medium --nodes=2 --applications=Traefik,Helm,kubernetes-dashboard --wait --save --switch
Building new Kubernetes cluster demo: Done
Created Kubernetes cluster demo in 03 min 11 sec
$
```
- The command will create the k8s cluster and save the kubeconfig file in `~/.kube/config`. You should be ready to run kubectl commands.
```
$ civo kubernetes show demo
                ID : 3a5c1291-12ba-42bf-bcfc-fa418b626bd8
              Name : demo
           # Nodes : 2
              Size : g2.medium
            Status : ACTIVE
           Version : 1.0.0
      API Endpoint : https://91.211.152.123:6443
      DNS A record : 3a5c1291-12ba-42bf-bcfc-fa418b626bd8.k8s.civo.com
                     *.3a5c1291-12ba-42bf-bcfc-fa418b626bd8.k8s.civo.com

Nodes:
+------------------+----------------+--------+
| Name             | IP             | Status |
+------------------+----------------+--------+
| kube-master-343b | 91.211.152.123 | ACTIVE |
| kube-node-4981   |                | ACTIVE |
+------------------+----------------+--------+

Installed marketplace applications:
+----------------------+------------+-----------+--------------+
| Name                 | Version    | Installed | Category     |
+----------------------+------------+-----------+--------------+
| kubernetes-dashboard | v2.0.0-rc7 | Yes       | management   |
| Helm                 | 2.14.3     | Yes       | management   |
| Traefik              | (default)  | Yes       | architecture |
+----------------------+------------+-----------+--------------+

$ kubectl.exe get nodes
NAME               STATUS   ROLES    AGE   VERSION
kube-master-343b   Ready    master   30m   v1.16.3-k3s.2
kube-node-4981     Ready    <none>   30m   v1.16.3-k3s.2
```
- You can also scale the cluster.
```
$ civo kubernetes scale demo --nodes=3
Kubernetes cluster demo will now have 3 nodes
```
- You can add more applications to the cluster.
```
$ civo applications add cert-manager --cluster=demo
Added cert-manager v0.11.0 to Kubernetes cluster demo
```
- Tearing down the cluster.
```
$ civo kubernetes remove demo
Removing Kubernetes cluster demo

$ civo kubernetes show demo
No Kubernetes clusters found for 'demo'. Please check your query.
```
- To play more with civo-cli, follow [this](https://www.civo.com/learn/kubernetes-cluster-administration-using-civo-cli).

