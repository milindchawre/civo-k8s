# ttl.sh: Your anonymous and ephemeral Docker image registry

| ![cka-ckad](https://github.com/milindchawre/civo-k8s/raw/master/blog/ttl-sh/images/ttl-sh.png) | 
|:--:| 
| *ttl.sh* |

[ttl.sh](https://ttl.sh/) is free to use, open source, anonymous and ephemeral docker image registry. It is developed by [replicated](https://www.replicated.com/). This guide looks at what an ephemeral and anonymous registry is, why you would want to use one, how to utilize ttl.sh, and some of its features.

### Why we need an ephemeral and anonymous registry

#### - Management Overhead

Docker registries are commonly used to store container images. While you can host your own image registry, doing so presents different maintenance challenges, like:

- Managing the infrastructure and bearing its cost
	
- Configuring proper authentication mechanisms to access registry
	
- Availability - keeping the registry always up
	
- Setting up an image cleanup policy to prevent running out of space
	
- The operational part of running the infrastructure, and so on.

Even a managed docker registry present some of these challenges.

#### - Authentication

A typical Continuous Integration (CI) workflow will build an image, push the image into a pre-defined registry, and the subsequent steps will perform tests on that image (either sequentially or in parallell). So one steps will build and push the image, while other subsequent distributed steps will pull that image. The most common-seen issue here is that the registries require authentication to push and pull images. As the official ttl.sh site says: "A CI workflow can either bake its own credentials and share them with build workers, or build workers can bring their own registry credentials. The first is insecure, the second creates friction for new contributors".

So, Overall there are [different challenges in maintaining a docker registry](https://dzone.com/articles/docker-registries-and-the-five-problems-you-encoun), some of which [ttl.sh](https://ttl.sh/) tries to solve by making an ephemeral and anonymous registry available. Let's look at what sets it apart from some other registries.

### Features of ttl.sh
- No login is required, lowering the barrier to access.
- You can anonymously push and pull images.
- Images are ephmeral and get deleted after a pre-defined time limit. The default limit is 1 hour, while the maximum is 24 hours. Valid time tags are in the format `:5m, :100s, :3h, :1d`. This means the images get cleaned up automatically.
- Image pulls are fast, deployed behind Cloudfare for rapid access across the globe, helping with the availability and operations challenges mentioned above.

### How to use ttl.sh
- First, tag your image with ttl.sh, a UUID, & time limit (i.e. `:2h,:60s,:30m`):

```
$ IMAGE_NAME=$(uuidgen)                              # generates UUID like "f4e58dc4-375d-4c9e-9435-8ec0eac51c19"
$ docker build -t ttl.sh/${IMAGE_NAME}:1h .          # generates full image path like ttl.sh/f4e58dc4-375d-4c9e-9435-8ec0eac51c19:1h
```

***Note:** You don't necessarily need to use an UUID for the image name; it can be anything. But since anyone can anonymously pull your image, its better to stick with UUID. We used `uuidgen` utility to generate the UUID. Your OS may have a different tool to generate them.*

- Then, push your image:

```
$ docker push ttl.sh/${IMAGE_NAME}:1h
.............................................................................
 image ttl.sh/f4e58dc4-375d-4c9e-9435-8ec0eac51c19:1h is available for 1 hour
```

- Now, you will be able to pull your image (before it expires):

```
$ docker pull ttl.sh/${IMAGE_NAME}:1h
```

### What can I use ttl.sh for?
ttl.sh can host your container images, so there are endless possibilities where you can use it, may it be. It could be:

- in your personal projects

- non-proprietary projects

- in your CI workflow (where build artifacts are short lived)

- in any open source project (especially in pull request build and validation)

***Note:** Images hosted on ttl.sh are publicly available out there. Anyone with the right image name can pull and use it. This is why we used the UUID in the image name. It provides some sort of secrecy.*

As an example, for a personal project, you could make changes to the code and have a CI/CD project build a container image based on the changes. This can automatically be pushed to ttl.sh and a [Kubernetes cluster can pull this version to a namespace](https://www.civo.com/learn/gitops-on-civo-gimlet-part-one), allowing you to immediately test the changes "live".

### Conclusion

Given the challenges of running a Docker image registry yourself, and the limitations placed by some registries on users, ttl.sh is a welcome tool on the landscape. You should always be conscious that while it only stores images for a pre-defined time and a maximum time of 1 day, they are available for anyone to pull if they know the image name. For this reason, you should only host non-private images on such a host.

For more information, check out the [ttl.sh project on GitHub](https://github.com/replicatedhq/ttl.sh).

If you found this guide useful, let me know on Twitter at [@milindchawre](https://twitter.com/milindchawre) or you can connect with me on [Linkedin](https://www.linkedin.com/in/milind-chawre).

