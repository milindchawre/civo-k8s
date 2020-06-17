#### Introduction

[Okteto](https://okteto.com) is an [open source](https://github.com/okteto/okteto) tool to develop and test your code directly in Kubernetes. In this guide will walk you through how to develop Java application on a Kubernetes cluster using Okteto.

##### Defining the Problem
Systems based on microservice architecture have many advantages like scalability, resilience, fault tolerance and so on. But they are also harder to setup locally as a development environment due to their very nature. You need to run several services with different runtimes on your local workstation. Also running these multiple services will eat away the available computer resources.

Kubernetes has become industry standard to run production workloads. But developing in Kubernetes presents some challenges. Typically, you would finish writing your code and package it in a Docker image, push that image to a registry, followed by pulling the container to your cluster for deployment, checking everything works as expected, and then repeating the process for new code. This is not only slow and tedious, but it means that it would be difficult to take advantage of useful features of tools such as debuggers, immediately-visible changes on reload, and so on.

##### Solution - a Development Platform in the Cloud
[Okteto](https://github.com/okteto/okteto) is an answer to your problem. Okteto transforms your Kubernetes cluster into a fully-featured development platform. It allows you to develop and test your code directly in Kubernetes while taking full advantage of tools like Maven and Gradle, popular frameworks, hot reloads, incremental builds, IDE debuggers and so on.

#### Application Development with Okteto
Lets see how to develop a [sample Java application](https://github.com/milindchawre/okteto-java-maven-demo) using Okteto.

*NOTE: This guide focus on developing Java application on Kubernetes, but there are other guides to develop with your favorite programming language such [Node](https://okteto.com/docs/samples/node), [Python](https://okteto.com/docs/samples/python), [Go](https://okteto.com/docs/samples/golang), [Ruby](https://okteto.com/docs/samples/ruby/index.html), etc.*

##### **Prerequisite**
Before you begin, you will need following things:
- Code Editor/IDE
- [kubernetes cluster](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)
- `KUBECONFIG` file pointing to your kubernetes cluster, where the application will be deployed.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) to communicate with your cluster.

*If you don't have kubernetes cluster ready, why not [sign up](https://www.civo.com/signup) to civo for the managed Kubernetes beta to try this out for yourself! During the beta, all users will get $70 worth of credit on their month-end statements, which is enough to run a three-node cluster 24/7 for the month.*

##### **Step 1: Deploying the Java App**
Get the source code of our sample Java app.
```
$ git clone https://github.com/milindchawre/okteto-java-maven-demo
$ cd okteto-java-maven-demo
```
*Note: The [kubernetes.yaml](https://github.com/milindchawre/okteto-java-maven-demo/blob/master/kubernetes.yaml) file contains the [kubernetes manifest](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) to deploy this app.*

Lets deploy it on Kubernetes.

```
# Make sure `KUBECONFIG` env is pointing to your kubernetes cluster
$ kubectl apply -f kubernetes.yaml
deployment.apps/demo created
```
Your sample Java application is now running on Kubernetes.

##### **Step 2: Install the Okteto CLI**
The [Okteto CLI](https://github.com/okteto/okteto) allows you to develop your applications directly in Kubernetes while taking advantage of well-known tooling for your particular choice of language. For Java, these include Maven/Gradle, Spring Boot and your favourite IDE's debugger functionality. It also speeds up our development cycle instead of going through the typical development workflow described above.

Install the [Okteto CLI](https://github.com/okteto/okteto) by running the following command:

*`MacOS/Linux`*
```
$ curl https://get.okteto.com -sSfL | sh
```
You can also install via [brew](https://brew.sh/) by running:
```
$ brew install okteto
```

*`Windows`*

Download the [executable](https://downloads.okteto.com/cli/okteto.exe) and add it to your `$PATH`. 

You can also install via [scoop](https://scoop.sh/) by running:
```
$ scoop install okteto
```

*`Github`*

Alternatively, you can directly download the binary [from GitHub](https://github.com/okteto/okteto/releases).

##### **Step 3: Start your development environment in Kubernetes**
With the sample Java application deployed as above, run the following command:
```
$ okteto up 

 +  Development environment activated
 ✓ Files synchronized
   Namespace: default
   Name: demo
   Forward: 8080 -> 8080
            8088 -> 8088

...
```
The command `okteto up` starts a [Kubernetes development environment](https://okteto.com/docs/reference/development-environment/index.html), which means:
- It connects to your remote kubernetes cluster
- Goes to the deployment that has your application and changes the docker image with a different one that contains the required dev tools to build, test, debug and run a Java application.
- Sets up automatic [file synchronization](https://okteto.com/docs/reference/file-synchronization) in both directions so that to keep your changes up-to-date between your local filesystem and your application pods.
- Sets up port-forwarding in both directions both for your app (8080 :- the application) and for your debugging tools (8088 :- the debugger).
- Setups a tiny SSH server in the pod to make it easy to work with SSH based tools (i.e. remote workspaces in Visual studio Code). Now you can build, test, and run your application as if you were doing it on your local machine.

You can customize the way Okteto manipulates your code, using [okteto.yml](https://github.com/milindchawre/okteto-java-maven-demo/blob/master/okteto.yml) [manifest file](https://okteto.com/docs/reference/manifest/index.html). You can also use the file `.stignore` to [skip files](https://okteto.com/docs/reference/file-synchronization/index.html#ignoring-files) from file synchronization. This is useful to save you from synchronising data that doesn't need to be moved.

The first time you run the application, Maven will compile your application. Wait for this process to finish.

Okteto automatically forwards port 8080 from your local computer to the remote development environment, making it accessible via localhost. Test your application by running the command below in a local shell:
```
$ curl localhost:8080
Hello world!
```
Or visit to `http://localhost:8080` on your browser. You should see the text `Hello World!` like in the image below:

![okteto1](https://github.com/milindchawre/civo-k8s/raw/master/blog/okteto/images/okteto1.png)

##### **Step 4: Develop directly in Kubernetes**
Open [src/main/java/com/example/demo/RestHelloWorld.java](https://github.com/milindchawre/okteto-java-maven-demo/blob/master/src/main/java/com/example/demo/RestHelloWorld.java) in your favorite local IDE and modify the response message on [line 11](https://github.com/milindchawre/okteto-java-maven-demo/blob/master/src/main/java/com/example/demo/RestHelloWorld.java#L11) to something else like to *Hello world from the cluster!*. Now save your changes.

![okteto2](https://github.com/milindchawre/civo-k8s/raw/master/blog/okteto/images/okteto2.png)

As soon as you save the code, your IDE will auto compile only the necessary `*.class` files that will be synchronized by Okteto to your application in Kubernetes. Take a look at the remote shell and notice how the changes are detected by Spring Boot and automatically hot reloaded. Please note that to enable Spring Boot hot reloads you need to import the `spring-boot-devtools` dependency in your application, which in the case of this tutorial, we already did [here](https://github.com/milindchawre/okteto-java-maven-demo/blob/master/pom.xml#L42-L45).

Call your application to validate the changes:
```
$ curl localhost:8080
Hello world from the cluster!
```

![okteto3](https://github.com/milindchawre/civo-k8s/raw/master/blog/okteto/images/okteto3.png)

Great! Your code changes are instantly applied to Kubernetes. No commit, build or push required! 😎

![okteto4](https://github.com/milindchawre/civo-k8s/raw/master/blog/okteto/images/okteto4.png)

The end result is that the remote Kubernetes cluster seems local to your workstation. When you edit your code on your workstation and as soon as you save it, the changes go to the remote cluster and your app instantly updates (taking advantage of any hot-reload mechanism if present like [springboot hot swapping](https://docs.spring.io/spring-boot/docs/2.0.x/reference/html/howto-hotswapping.html) in our case). This whole process happens in an instant. There is no need to build any docker image or apply any kubernetes manifests. All of this is taken care by Okteto.

##### **Step 5: Debug directly in Kubernetes**
Okteto also allows you to debug your applications directly from your IDE. Let’s see how it works in [VS Code](https://code.visualstudio.com/). To enable debug mode, define the following as a [JVM arguments](https://github.com/milindchawre/okteto-java-maven-demo/blob/master/pom.xml#L54-L56) in your Maven configuration files:
```
-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8088
```

Open [launch.json](https://code.visualstudio.com/docs/java/java-debugging#_configure) to modify the debug configuration. Add [configuration](https://code.visualstudio.com/docs/java/java-debugging#_attach) to attach to a remote debugger.

![okteto5](https://github.com/milindchawre/civo-k8s/raw/master/blog/okteto/images/okteto5.png)

Now, add a breakpoint in your code and click on Run Debug. Go back to the browser and reload the page. The code execution will halt at your breakpoint. You can now inspect the code, trace the function calls, look at available variables, and so on.

![okteto6](https://github.com/milindchawre/civo-k8s/raw/master/blog/okteto/images/okteto6.png)

So the code runs in remote Kubernetes cluster, but you can debug it from your local workstation. 😉

##### **Step 6: Wrapping Up**
Once you are done with the development, run [okteto down](https://okteto.com/docs/reference/cli/index.html#down) command. It deactivates your development environment, stops the file synchronization service and restores your previous deployment configuration.
```
$ okteto down
 +  Development environment deactivated
 i  Run 'okteto push' to deploy your code changes to the cluster
```

##### Okteto Cloud
It's possible to use Okteto for local development even when you don't have your own kubernetes cluster. [Okteto Cloud](https://cloud.okteto.com/#/login) gives you free access to secure Kubernetes namespaces, fully integrated with remote development capabilities. For more info, refer the official [documentation](https://okteto.com/docs/cloud/index.html).

![okteto7](https://github.com/milindchawre/civo-k8s/raw/master/blog/okteto/images/okteto7.png)

There is also a hosted version called [Okteto enterprise](https://okteto.com/enterprise/) that companies can use on their own premises instead of Okteto Cloud.

##### Conclusion
Okteto transforms your Kubernetes cluster into a fully-featured development platform. It lets you develop your applications directly in Kubernetes. This way it helps in lot of different ways:
- You don't need to install compilers, runtimes, docker or kubernetes locally on your workstation anymore.
- No need to build docker images and create deployments everytime you make changes in the code.
- Test your application as fast as you can type, resulting in a faster feedback loop.
- No more compute resources wasted in your machine. The resources are limited by the power of the cloud.
- While developing with Okteto you can still use your favorite IDE and debugging/testing tools with features like incremental builds and hot-reloads as supported by your programming language.
- The remote Kubernetes clusters can be used as a local filesystem/environment on your workstation.
- You avoid the `works on my machine` problem as your application is developed inside the cluster in which it is destined to be deployed.
- It solves challenges faced by multiple developers when working simultaneously on a common set of services. Having a separate dedicated Kubernetes namespace for each developer helps in resolving many issues.
- Reduce integration issues by developing in a more production-like environment.

For more information, check out the [Okteto project on GitHub](https://github.com/okteto/okteto) and refer to this [quickstart guide](https://okteto.com/docs/getting-started/index.html). Also check out the [Okteto samples repository](https://github.com/okteto/samples) to see how to use Okteto with different programming languages and debuggers.

If you found this guide useful, let us know on twitter at [@civocloud](https://twitter.com/civocloud)! You can also reach me out on twitter at [@milindchawre](https://twitter.com/milindchawre).
