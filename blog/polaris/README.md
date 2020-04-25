[Polaris](https://www.fairwinds.com/polaris) is an open-source project that looks for configuration issues in kubernetes that can affect stability, reliability, scalability, and security. It was built by [Fairwinds (formerly ReactiveOps)](https://www.fairwinds.com/).

#### Problem
Creating cluster is easy, but running it at scale with stability and security is hard. We have seen this often a small mistake in deployment configuration can later result in bigger issues. Something like missing to configure resource requests can break auto scaling or even cause workloads to run out of resources. Polaris aims to catch and prevent such problems.

#### Polaris Features
- Dashboard for auditing Kubernetes workload configuration
- Cli utility for auditing k8s yamls
- polaris webhook that prevents future deployments if it doesn't meets the configured standard
- audits more than just k8s resources like container health checks, image tags, networking, security settings, etc

#### Installation
The polaris dashboard can be [installed](https://github.com/FairwindsOps/polaris/blob/master/docs/usage.md#installing) using `kubectl`, `helm` or `local binary`.
- kubectl
```
kubectl apply -f https://github.com/fairwindsops/polaris/releases/latest/download/dashboard.yaml
```
- helm
```
helm repo add fairwinds-stable https://charts.fairwinds.com/stable
helm upgrade --install polaris fairwinds-stable/polaris --namespace polaris
```
- binary : download the binary from [release page](https://github.com/fairwindsops/polaris/releases)

then use port-forward to access the dashboard:
```
kubectl port-forward --namespace polaris svc/polaris-dashboard 8080:80
```
and visit `http://localhost:8080/` to view the dashboard.

![polaris1](https://github.com/milindchawre/civo-k8s/raw/master/blog/polaris/images/polaris1.png)

As shown in the dashboard, the polaris gives a `grade` and `score` to your kubernetes cluster based on the configuration of your workloads. You can now work to improve the workload configuration to improve your cluster `grade` and `score`. This will help in making your cluster more secure, stable, scalable, and resilient.

The dashboard also provides a  high-level summary of checks for each category with some helpful information.

![polaris2](https://github.com/milindchawre/civo-k8s/raw/master/blog/polaris/images/polaris2.png)

You can also see kubernetes deployments with specific misconfigurations listed.

![polaris3](https://github.com/milindchawre/civo-k8s/raw/master/blog/polaris/images/polaris3.png)

As shown this [nginx-deployment](https://raw.githubusercontent.com/milindchawre/civo-k8s/master/blog/polaris/nginx.yaml) have few misconfigurations like image tag is not specified, resources like cpu and memory are missing, health checks are not configured and so on. Let's try to fix few of them.

Polaris also shows the meaning of each configuration and what config is missing with some reference links explaining the use and importance of those configuration.

![polaris4](https://github.com/milindchawre/civo-k8s/raw/master/blog/polaris/images/polaris4.png)

![polaris5](https://github.com/milindchawre/civo-k8s/raw/master/blog/polaris/images/polaris5.png)

Now apply the new [nginx-deployment](https://raw.githubusercontent.com/milindchawre/civo-k8s/master/blog/polaris/nginx-fix.yaml) where we had changed few things to fix few of the misconfigurations.
```
17c17
-         image: nginx:latest
---
+         image: nginx:1.18.0                        # Changed image tag from latest to specific release
19a20,50
+         resources:                                 # Added resource request and limits for cpu and memory
+           limits:
+             memory: "200Mi"
+             cpu: "0.5"
+           requests:
+             memory: "100Mi"
+             cpu: "0.2"
+         livenessProbe:                             # Added readiness and liveness probe
+           httpGet:
+             path: /
+             port: 80
+             httpHeaders:
+           initialDelaySeconds: 10
+           periodSeconds: 3
+           timeoutSeconds: 1
+           successThreshold: 1
+           failureThreshold: 3
+         readinessProbe:
+           httpGet:
+             path: /
+             port: 80
+           initialDelaySeconds: 10
+           periodSeconds: 3
+           timeoutSeconds: 1
+           successThreshold: 1
+           failureThreshold: 3
```

Now check the nginx-deployment in polaris dashboard, few of the mis-configurations should have gone.

![polaris6](https://github.com/milindchawre/civo-k8s/raw/master/blog/polaris/images/polaris6.png)

Fixing such mis-configuration for all the workloads will improve the `grade` and `score` of your cluster that can be seen at the top of polaris dashboard. This will make your cluster more secure, stable, scalable, and resilient.

#### Polaris Cli 
If you don't want to deploy polaris in your kubernetes cluster as an another application running along with the other workloads, then make use of polaris cli. With the cli you can audit the k8s yaml ansd also view the polaris dashboard locally.

#### Polaris Webhook
The polaris webhook provides a way to enforce some standards in all the kubernetes deployments. Once you have addressed all the misconfigurations identified in polaris dashboard, you can now deploy the polaris webhook to ensure that the configuration never slips below the configured standard. Once you deploy it in the cluster, the webhook will prevent any further kubernetes deployment that doesn't meet the configuration standard.

#### Polaris in CI/CD pipelines
Polaris can be integrated in your CI/CD pipelines.
```
polaris audit --audit-path path/to/my/deployment/yaml --set-exit-code-on-error --set-exit-code-below-score 90
```

For more information, check out the [Polaris project on GitHub](https://github.com/FairwindsOps/polaris) and this intro [video](https://www.youtube.com/watch?v=Vo4PBG6Vj0Q) for polaris.

