# kubernetes-gitops-with-argocd
LinkedIn Learning Course - Kubernetes: GitOps with ArgoCD

https://www.linkedin.com/learning/kubernetes-gitops-with-argocd

## Introduction

### Continuous delivery of infrastructure with Argo CD

Argo CD - A declarative, GitOps continuous delivery tool for Kubernetes

Argo CD allows for CD of infrastructure

Originally open-source by Intuit in 2018

Hi, and welcome to this Kubernetes Essential Training: GitOps with Argo CD. Argo CD is a declarative GitOps continuous delivery tool for Kubernetes. CD here stands for continuous delivery and in Argo CD, this means continuous delivery of infrastructure. Argo CD was originally open source by Intuit in January, 2018. And it's used by a large number of companies around the world. Now you're probably familiar with the idea of CI/CD that is continuous integration and continuous delivery pipelines for code. This is where incremental code changes that you make to your application are built and tested in an automated manner. CI/CD for code is an integral part of the DevOps mindset and philosophy. And it's also essential for agile development. Here code that developers write are checked in and pushed out to production as soon as possible. And this requires the entire pipeline to be automated. This is what allows code to be delivered to production environments quickly and seamlessly. The idea of CI/CD pipelines for your applications code has been around a while and has been implemented by many organizations. Now organizations today are moving to the cloud as fast as they can. Cloud native applications are those that are built natively for the scalable elastic nature of the cloud. Cloud native applications typically do not use manual techniques for provisioning of resources. Cloud native applications use infrastructure-as-code for deployments. The whole idea behind infrastructure-as-code is to make release engineering easier and more automated. You shouldn't be managing and provisioning your resources manually. Instead, you should be using configuration files to define the physical infrastructure that you want to set up. The network resources you want your application to use. And also the security configuration. All of these should be declaratively specified in config files and should be stored in a version control system, such as GitHub, just like you do with the applications code. If infrastructure is declaratively specified and stored in a version control system, why should we not use continuous delivery for infrastructure? And this is exactly what Argo CD does. Any change you make to your infrastructure specification in a data repository will be automatically reflected in your live production environment. This continuous delivery of infrastructure is only possible because of the extensive use of infrastructure as code in cloud native applications. In infra-as-code, applications configurations, and even environments are not only declarative, they're also version controlled. When you use Argo CD for continuous delivery of infrastructure, it's possible to fully automate your application deployment and life cycle management. Your automation is not reserved only for your code pipelines. You can also apply this automation to your infrastructure pipelines. Argo CD fully automates your deployment, making them seamless, less error prone, and more robust.

### Quick prerequisite technology overview

Before we dive headfirst into working with Argo CD, let's take a quick look at the technologies that you need to be familiar with to make the most of your learning. You should be comfortable working with **Git repositories**. We'll be using **GitHub** to store our infrastructure specifications. You should be comfortable containerizing apps using **Docker**. And you should know how you can manage container clusters using **Kubernetes** and how we deploy apps to Kubernetes. Argo CD integrates with Git repositories and uses Git as the source of truth for how infrastructure is defined.

#### Git
Git is a free and open-source distributed version control system that can handle small projects as well as large ones. In this course, we'll be using GitHub to store the manifest definitions of our infrastructure.

#### Docker

Another technology that you need to be familiar with is Docker. Docker helps deliver software in the form of containerized applications. Containers bundle together software, dependencies, and config files, so a container contains everything that your application needs to run. Containers are lightweight, isolated units which communicate with other containers in a well-defined manner. The most popular containerization technology in use today is Docker, and you should be comfortable with building Docker images and registering them with Docker Hub or some other container registry. 

#### Kubernetes

Argo CD is meant specifically to build continuous delivery pipelines for infrastructure that is deployed to Kubernetes. Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services. Standalone containers are not very useful. Kubernetes allows us to convert our containers running our apps to container clusters. Kubernetes is the orchestration software used to manage container clusters. You can use Kubernetes to deploy containerized applications that are scalable, auto-healing, and load-balanced. And an important part of Kubernetes is the declarative configuration of your infrastructure and the use of automation in your releases and deployments. The nature of infrastructure specified on Kubernetes makes it very seamless for us to add on continuous delivery. 

**Argo CD is built on top of Docker and Kubernetes**, and a solid understanding of these technologies is important before you can work with Argo CD.

## 1. Getting Started with Argo CD

### GitOps for infrastructure automation

#### GitOps

Argo CD is used for GitOps. What exactly is GitOps? This is a term that was **coined as lately as 2017 by Alexis Richardson, who works at Weaveworks**. Let's define 

GitOps first: a way of implementing continuous deployment, or continuous delivery, for cloud-native applications by using Git as a source of truth for declarative infrastructure and applications. 

Weaveworks defines GitOps as an operating model for Kubernetes and other cloud-native technologies. 

GitOps provides a set of best practices that unify Git deployment, management, and monitoring for containerized clusters and applications. 

The basic idea behind GitOps is that the continuous integration and continuous delivery or deployment practices that we apply to code should also be applied to infrastructure. This means that both the development as well as the operations team within any organization can work with fully automated workflows. 

GitOps takes the DevOps philosophy and mindset one step further by applying it to operations, not just code. 


Here is a high-level visualization of how GitOps works. Now, there is a lot going on here, so let's break down this diagrammatic representation. The pipeline that you see here on top is actually your CI/CD pipeline for code. A developer writes code and commits it to some kind of repository. The continuous integration pipeline will then run automated tests, build an image based on your application, and register it with, say, some container registry. At the same time, your operations team can then use infrastructure as code practices to declaratively specify what the infrastructure looks like for your application. This infrastructure will be stored in some kind of state repository, and your GitOps automation should be able to sync state from this infrastructure state repository, as well as sync code from your container registry for the final deployment. 

##### The basic principles of GitOps
requires that your infrastructure be described declaratively and be version controlled. 

You use Git, some kind of Git repository, as the source of truth. Any changes you make to your infrastructure is pushed to Git, and this is automatically deployed to production. Of course, you then have to ensure that you're constantly monitoring your production environment for correctness. Use alerts to warn you of failures. 

##### Benefits of GitOps

The use of GitOps automates the entire life cycle of your application and results in tremendous **productivity improvements for infra deployment**. It **improves the overall stability of your production system** by giving you very tight control over what exactly is provisioned and deployed. You get **better reliability because you're versioning your infrastructure specification**, and you get **consistency and standardization of your infra**.

### Introducing Argo CD

We've only had a quick introduction to Argo CD. The declarative GitOps continuous delivery tool for Kubernetes. Here, we'll dig a little deeper. 

#### How exactly does Argo CD work? 

**Argo CD uses Git repositories as the source of truth. An infrastructure is described using Kubernetes manifest**. 

##### Git repositories as the source of truth 

Let's first talk about using Git repositories as the source of truth. **With Argo CD, all of your Kubernetes manifest files, which are the configuration file for your infrastructure, are stored in Git and these serve as the source of truth**. These configuration files **define the desired end state of your application**. Argo CD looks at the infrastructure specification that you have stored in Git and at the life state of your application and runs a synchronization process to ensure that life state matches the desired state. Argo CD, in addition, can **track updates that you meet to various branches, the tags that you use on your gate repositories and the versions.** 

##### Infrastructure is described using Kubernetes manifest

You might be using a number of different technologies and utilities to manage your Kubernetes manifest files. And Argo CD integrates with a large number of these uses. With Argo CD, you can **describe your infrastructure using kustomize applications, helm charts, ksonnet and jsonnet applications, or just plain old YAML or json files.** If you have a custom config management tool that you're using with your Arg, you can configure plugins to use that with Argo CD.

### Argo CD for continuous integration of infrastructure

Let's see how Argo CD works to give us continuous integration and continuous delivery of infrastructure. Here is a big picture overview. This overview gives us both the code pipeline, as well as the infrastructure automation pipeline. A developer writes code and then commits this code to see a git repository. A continuous integration pipeline picks up the changes that the developer makes and runs automated tests and builds images, which contain the final application. These images are typically stored in an image repository, such as Docker hub or the container registries of cloud platforms. Now the same developer or a release engineer will update the configuration that correspond to your infrastructure specification. 

A continuous integration pipeline can now be run on our infra as code specifications. **Argo CD will be responsible for this continuous integration pipeline and all of your Kubernetes manifest specifications, which describe your infrastructure will be deployed to a Kubernetes cluster**. Argo CD can be configured to **manage separate deployments for each of your environments in dev, test, and prod**. Argo CD is thus **responsible for the continuous integration pipeline that you use for your infrastructure automation**.

### Creating a local K3s cluster

```bash
brew install docker kubectl k3d
```

In order to use Argo CD for continuous delivery of our Kubernetes infrastructure, I'm going to install Kubernetes on my local machine. That will be the cluster that we'll run Argo CD and deploy our apps. I already have Docker installed on my local machine and you can see that I have no containers running within Docker. In order to work with Kubernetes, I'm going to set up 

**kubectl. This is the Kubernetes command line tool that'll allow me to run commands against my local Kubernetes cluster.** 

I'm using "brew install" for this because I'm running on a Mac machine. 

**Because we're working with Kubernetes on our local machine, instead of installing the heavyweight Kubernetes setup, we'll use k3d, which is a lightweight wrapper to run k3s.**

**k3s is an official CNCF sandbox project that gives you a lightweight but powerful Kubernetes distribution, designed for production workloads, but can also be run in a development environment.** 

"brew install k3d" will install the k3d wrapper, and you can see that the k3d version at the time of this recording is v4.4.5. k3d is a utility that makes it very easy to create single and multi-node k3s clusters within Docker. We already have Docker installed. k3d helps you to create and manage k3s clusters for local development, development on the local machine. Here I am within my Desktop folder, and I'm going to create a new subfolder called Argo CD. This Argo CD folder will be my current working directory. "Desktop/ArgoCD" is where I'll be running most of my commands. If I'm running commands in a different subfolder, I show you with subfolder. The first step is to create a Kubernetes cluster. And for that, I'm going to create a cluster configured yaml file that contains the cluster specifications. We'll keep this cluster very, very simple. It'll have one server that runs the control plane and two agents that will run the kubelets. Let's go ahead and save this file and I'll do this using Control + X, and then choose Y to save this file.


We'll now create a new Kubernetes cluster using the cluster specification in cluster-config.yaml. The name of the cluster is argocd-cluster. This is the Kubernetes cluster that will contain the Argo CD controller that'll manage the continuous delivery. We'll also deploy our infrastructure components and our application to this same cluster. So Argo CD will run on the same cluster where we deploy our app. 

```bash
k3d cluster create argocd-cluster --config ./cluster-config.yaml

victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % k3d cluster create argocd-cluster --config ./cluster-config.yaml
INFO[0000] Using config file ./cluster-config.yaml (k3d.io/v1alpha2#simple) 
WARN[0000] Default config apiVersion is 'k3d.io/v1alpha5', but you're using 'k3d.io/v1alpha2': consider migrating. 
INFO[0003] Prep: Network                                
INFO[0003] Created network 'k3d-argocd-cluster'         
INFO[0003] Created image volume k3d-argocd-cluster-images 
INFO[0003] Starting new tools node...                   
INFO[0004] Creating node 'k3d-argocd-cluster-server-0'  
INFO[0005] Pulling image 'ghcr.io/k3d-io/k3d-tools:5.6.0' 
INFO[0007] Pulling image 'docker.io/rancher/k3s:v1.27.5-k3s1' 
INFO[0007] Starting Node 'k3d-argocd-cluster-tools'     
INFO[0021] Creating node 'k3d-argocd-cluster-agent-0'   
INFO[0021] Creating node 'k3d-argocd-cluster-agent-1'   
INFO[0021] Creating LoadBalancer 'k3d-argocd-cluster-serverlb' 
INFO[0022] Pulling image 'ghcr.io/k3d-io/k3d-proxy:5.6.0' 
INFO[0029] Using the k3d-tools node to gather environment information 
INFO[0029] Starting new tools node...                   
INFO[0029] Starting Node 'k3d-argocd-cluster-tools'     
INFO[0030] Starting cluster 'argocd-cluster'            
INFO[0030] Starting servers...                          
INFO[0031] Starting Node 'k3d-argocd-cluster-server-0'  
INFO[0034] Starting agents...                           
INFO[0034] Starting Node 'k3d-argocd-cluster-agent-0'   
INFO[0034] Starting Node 'k3d-argocd-cluster-agent-1'   
INFO[0038] Starting helpers...                          
INFO[0038] Starting Node 'k3d-argocd-cluster-serverlb'  
INFO[0045] Injecting records for hostAliases (incl. host.k3d.internal) and for 5 network members into CoreDNS configmap... 
INFO[0047] Cluster 'argocd-cluster' created successfully! 
INFO[0047] You can now use it like this:                
kubectl cluster-info
```

We'll wait for the cluster to come up. I'm going to head over to Docker desktop, and there you can see that our server and two agents are all running. And everything looks good. Cluster Argo CD has been created successfully. 

I now use kubectl to verify that I can communicate with my cluster. "kubectl get nodes" should get me the nodes of this cluster. There you can see it. One server and two agents, and all of them have the status "ready". Everything looks good. 

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl get nodes
NAME                          STATUS   ROLES                  AGE   VERSION
k3d-argocd-cluster-server-0   Ready    control-plane,master   50s   v1.27.5+k3s1
k3d-argocd-cluster-agent-1    Ready    <none>                 46s   v1.27.5+k3s1
k3d-argocd-cluster-agent-0    Ready    <none>                 47s   v1.27.5+k3s1
```

A quick look at the kube config file will give us configuration details of this cluster. Here is our k3d-argocd-cluster successfully set up.

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % cat ~/.kube/config | grep name
  name: k3d-argocd-cluster
  name: k3d-argocd-cluster
- name: admin@k3d-argocd-cluster
```

### Architectural overview

Before we get hands-on with Argo CD, let's get a quick architectural overview of the various components that make up Argo CD and how they work together.

Argo CD is made up of three main components:

* **The API Server**

	- responsible for application management and status reporting
	- Argo CD manages infrastructure deployment using applications. All of the Argo CD applications operations such as **sync**, **rollback** and **any user-defined actions** that you set up are managed by the API server
	- exposes gRPC APIs that clients can connect to
	- such as Jenkins or some other automation tool
	- manages the credentials for the repositories that you connect to. And any clusters that you connect to, to deploy apps.
	- responsible for authentication and auth delegation to external identity providers. It follows role-based access control (RBAC) enforcement and also has the listener and forwarder for Git Webhook events.

- **The Repository Service (Server)**

	- manages the connections with Git repositories, which can be in our source of truth
	- The repository service connects to git repositories to check what the desired state of the applications is. Git repositories, use Webhook Events to notify Argo CD, when new change are committed.
	- internal service that maintains a local cache of the Git repository, holding the application manifest
		- these manifests can be in the form of customized applications, helm charts, jsonnet files, Argo CD supports a number of different Kubernetes utilities

- **The Application Controller**
	- a Kubernetes controller, which syncs the desired state of the application, with the current life state
	- a Kubernetes cluster that runs continuously monitoring the application that you have deployed
	- compares the current life state of your application against the target desired state that is specified in the Git repository
	- It will be constantly monitoring your applications state and it'll detect when it's out of sync with what's in the Git repo
	- can optionally be configured to take corrective action automatically
	- responsible for invoking any user-defined hooks for life cycle events


You can interact with Argo CD using a web interface that is the UI or command line interface.

Argo CD is built on top of Kubernetes and works with Kubernetes clusters. Any change we make to our Kubernetes manifest are deployed to Kubernetes.

## 2. Working with Argo CD on a Local Kubernetes Cluster

### Installing Argo CD services and the CLI

We are now ready to install and run the Argo CD application on this cluster that we just created. The Argo CD application is a Kubernetes controller that manages the continuous delivery. "kubectl get namespace" shows me that there is a default namespace and a number of other Kubernetes-related namespaces. I'm going tocreate a new namespace called Argo CD. This is the namespace where the Argo CD services and application resources will live. "kubectl get namespace" shows us that the namespace has been successfully created. The specification for the Argo CD applications running on Kubernetes is present in a yaml file hosted on GitHub. So here is the install.yaml file. This is of type "CustomerResourceDefinition". This is the manifest for this CRB that defines the Kubernetes resources that we need to run Argo CD. Now this is auto-generated. You don't need to worry about what it contains, but you do need to know how to use it. Let's use "kubectl apply" to get Argo CD services running on our cluster. **By default this install.yaml file installs Argo CD in the Argo CD namespace. So if you want to change your namespace, you need to edit this install.yaml file.** Once the installation has been completed successfully, you can take a look at the deployment that Argo CD needs. I'm going to run "kubectl get deployment" in the Argo CD namespace, and you can see that there are four deployments. The argocd-server is the API server, which exposes the API consumed by the web user interface for Argo CD. The argocd-repo-server manages the GitHub repositories that we are connected to. The argocd-dex-server and the argocd-redis deployment manages an in-memory database that Argo CD uses. You can see that these deployments are not yet in the ready state. Next, let's take a look at the services installed by Argo CD using "kubectl argocd get service" and you can see that there are a number of different services that is a service for metrics. One thing to observe here is that none of the services have an external IP address. If you look at the service type, you'll see that every service exposes a cluster internal IP address. This makes each of these services only reachable from within the Kubernetes cluster, not externally. We've discussed that Argo CDs implemented as a Kubernetes controller, which continuously monitors running applications, to see whether they match the desired state. This Kubernetes controller is a statefulset object, the Argo CD application controller. Again, it's not yet ready. As we wait for the Argo CD services deployments and controllers to be ready, let's go ahead and install the command line interface for Argo CD. If you're not running on MacOS, Argo CD command line installation instructions are available here at this URL on argoproj.github.io. Once the command line has been installed, let's take a look at the Argo CD version that we are working with. Run "argocd version" and you can see that the version here is v2.0.3. This is the latest version at the time of this recording, but there seems to be an error here at the bottom that says "Argo CD server address unspecified." This is because, by default, the Argo CD API server, which the user interface will connect to, is not exposed with an external IP address. It only has an internal IP address. We'll fix this in just a bit. Let's take a look at the client version of Argo CD. That is our command line interface. And you can see that it's v2.0.3. There is no error here because we're not connecting to the API server to view the client version. Now that we have the client installed, let's quickly check to see whether the deployments and services are running. And here you can see, everything has the status "running". You're ready to use Argo CD.

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl get namespace
NAME              STATUS   AGE
kube-system       Active   59m
default           Active   59m
kube-public       Active   59m
kube-node-lease   Active   59m
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl create namespace argocd
namespace/argocd created
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl get namespace          
NAME              STATUS   AGE
kube-system       Active   64m
default           Active   64m
kube-public       Active   64m
kube-node-lease   Active   64m
argocd            Active   23s
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl apply --namespace argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/applicationsets.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io created
serviceaccount/argocd-application-controller created
serviceaccount/argocd-applicationset-controller created
serviceaccount/argocd-dex-server created
serviceaccount/argocd-notifications-controller created
serviceaccount/argocd-redis created
serviceaccount/argocd-repo-server created
serviceaccount/argocd-server created
role.rbac.authorization.k8s.io/argocd-application-controller created
role.rbac.authorization.k8s.io/argocd-applicationset-controller created
role.rbac.authorization.k8s.io/argocd-dex-server created
role.rbac.authorization.k8s.io/argocd-notifications-controller created
role.rbac.authorization.k8s.io/argocd-server created
clusterrole.rbac.authorization.k8s.io/argocd-application-controller created
clusterrole.rbac.authorization.k8s.io/argocd-applicationset-controller created
clusterrole.rbac.authorization.k8s.io/argocd-server created
rolebinding.rbac.authorization.k8s.io/argocd-application-controller created
rolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller created
rolebinding.rbac.authorization.k8s.io/argocd-dex-server created
rolebinding.rbac.authorization.k8s.io/argocd-notifications-controller created
rolebinding.rbac.authorization.k8s.io/argocd-server created
clusterrolebinding.rbac.authorization.k8s.io/argocd-application-controller created
clusterrolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller created
clusterrolebinding.rbac.authorization.k8s.io/argocd-server created
configmap/argocd-cm created
configmap/argocd-cmd-params-cm created
configmap/argocd-gpg-keys-cm created
configmap/argocd-notifications-cm created
configmap/argocd-rbac-cm created
configmap/argocd-ssh-known-hosts-cm created
configmap/argocd-tls-certs-cm created
secret/argocd-notifications-secret created
secret/argocd-secret created
service/argocd-applicationset-controller created
service/argocd-dex-server created
service/argocd-metrics created
service/argocd-notifications-controller-metrics created
service/argocd-redis created
service/argocd-repo-server created
service/argocd-server created
service/argocd-server-metrics created
deployment.apps/argocd-applicationset-controller created
deployment.apps/argocd-dex-server created
deployment.apps/argocd-notifications-controller created
deployment.apps/argocd-redis created
deployment.apps/argocd-repo-server created
deployment.apps/argocd-server created
statefulset.apps/argocd-application-controller created
networkpolicy.networking.k8s.io/argocd-application-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-applicationset-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-dex-server-network-policy created
networkpolicy.networking.k8s.io/argocd-notifications-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-redis-network-policy created
networkpolicy.networking.k8s.io/argocd-repo-server-network-policy created
networkpolicy.networking.k8s.io/argocd-server-network-policy created
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl --namespace argocd get deployment         
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
argocd-redis                       1/1     1            1           104s
argocd-applicationset-controller   1/1     1            1           104s
argocd-notifications-controller    1/1     1            1           104s
argocd-dex-server                  1/1     1            1           104s
argocd-repo-server                 1/1     1            1           104s
argocd-server                      1/1     1            1           104s
```

The `argocd-server` is the API server, which exposes the API consumed by the web user interface for Argo CD. The `argocd-repo-server` manages the GitHub repositories that we are connected to. The `argocd-dex-server` and the `argocd-redis` deployment manages an in-memory database that Argo CD uses. You can see that these deployments are not yet in the ready state.

The `argocd-applicationset-controller` ApplicationSet controller is a [Kubernetes controller](https://kubernetes.io/docs/concepts/architecture/controller/) that adds support for an `ApplicationSet` [CustomResourceDefinition](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) (CRD). This controller/CRD enables both automation and greater flexibility managing [Argo CD](https://argo-cd.readthedocs.io/en/stable/) Applications across a large number of clusters and within monorepos, plus it makes self-service usage possible on multitenant Kubernetes clusters.

Argo CD Notifications continuously monitors Argo CD applications and provides a flexible way to notify users about important changes in the application state. Using a flexible mechanism of [triggers](https://argo-cd.readthedocs.io/en/latest/operator-manual/notifications/triggers/) and [templates](https://argo-cd.readthedocs.io/en/latest/operator-manual/notifications/templates/) you can configure when the notification should be sent as well as notification content. Argo CD Notifications includes the [catalog](https://argo-cd.readthedocs.io/en/latest/operator-manual/notifications/catalog/) of useful triggers and templates. So you can just use them instead of reinventing new ones.

Next, let's take a look at the services installed by Argo CD

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl --namespace argocd get service
NAME                                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
argocd-applicationset-controller          ClusterIP   10.43.102.157   <none>        7000/TCP,8080/TCP            23m
argocd-dex-server                         ClusterIP   10.43.37.135    <none>        5556/TCP,5557/TCP,5558/TCP   23m
argocd-metrics                            ClusterIP   10.43.29.4      <none>        8082/TCP                     23m
argocd-notifications-controller-metrics   ClusterIP   10.43.53.231    <none>        9001/TCP                     23m
argocd-redis                              ClusterIP   10.43.149.191   <none>        6379/TCP                     23m
argocd-repo-server                        ClusterIP   10.43.51.146    <none>        8081/TCP,8084/TCP            23m
argocd-server                             ClusterIP   10.43.231.39    <none>        80/TCP,443/TCP               23m
argocd-server-metrics                     ClusterIP   10.43.218.83    <none>        8083/TCP                     23m
```

One thing to observe here is that none of the services have an external IP address. If you look at the service type, you'll see that every service exposes a cluster internal IP address. This makes each of these services only reachable from within the Kubernetes cluster, not externally.

We've discussed that Argo CDs implemented as a Kubernetes controller, which continuously monitors running applications, to see whether they match the desired state. This Kubernetes controller is a statefulset object, the Argo CD application controller.

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl -n argocd get statefulset
NAME                            READY   AGE
argocd-application-controller   1/1     66m
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % brew install argocd
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % argocd version
argocd: v2.10.1+a79e0ea.dirty
  BuildDate: 2024-02-14T22:23:04Z
  GitCommit: a79e0eaca415461dc36615470cecc25d6d38cefb
  GitTreeState: dirty
  GoVersion: go1.21.7
  Compiler: gc
  Platform: darwin/arm64
FATA[0000] Argo CD server address unspecified           
```

there seems to be an error here at the bottom that says "Argo CD server address unspecified." This is because, by default, the Argo CD API server, which the user interface will connect to, is not exposed with an external IP address. It only has an internal IP address.


Let's take a look at the client version of Argo CD. That is our command line interface. And you 
```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % argocd version --client
argocd: v2.10.1+a79e0ea.dirty
  BuildDate: 2024-02-14T22:23:04Z
  GitCommit: a79e0eaca415461dc36615470cecc25d6d38cefb
  GitTreeState: dirty
  GoVersion: go1.21.7
  Compiler: gc
  Platform: darwin/arm64
```

can see that it's v2.0.3. There is no error here because we're not connecting to the API server to view the client version. 

Now that we have the client installed, let's quickly check to see whether the deployments and services are running. 

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl -n argocd get all
NAME                                                    READY   STATUS    RESTARTS   AGE
pod/argocd-redis-69f8795dbd-4g4ql                       1/1     Running   0          78m
pod/argocd-applicationset-controller-596997cb6d-gjgmd   1/1     Running   0          78m
pod/argocd-notifications-controller-5c4c876d56-47zrj    1/1     Running   0          78m
pod/argocd-dex-server-c8cb9bd99-vjdt4                   1/1     Running   0          78m
pod/argocd-repo-server-c8f5bd5c5-d8cmc                  1/1     Running   0          78m
pod/argocd-application-controller-0                     1/1     Running   0          78m
pod/argocd-server-6bdd44685d-jcr98                      1/1     Running   0          78m

NAME                                              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/argocd-applicationset-controller          ClusterIP   10.43.102.157   <none>        7000/TCP,8080/TCP            78m
service/argocd-dex-server                         ClusterIP   10.43.37.135    <none>        5556/TCP,5557/TCP,5558/TCP   78m
service/argocd-metrics                            ClusterIP   10.43.29.4      <none>        8082/TCP                     78m
service/argocd-notifications-controller-metrics   ClusterIP   10.43.53.231    <none>        9001/TCP                     78m
service/argocd-redis                              ClusterIP   10.43.149.191   <none>        6379/TCP                     78m
service/argocd-repo-server                        ClusterIP   10.43.51.146    <none>        8081/TCP,8084/TCP            78m
service/argocd-server                             ClusterIP   10.43.231.39    <none>        80/TCP,443/TCP               78m
service/argocd-server-metrics                     ClusterIP   10.43.218.83    <none>        8083/TCP                     78m

NAME                                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/argocd-redis                       1/1     1            1           78m
deployment.apps/argocd-applicationset-controller   1/1     1            1           78m
deployment.apps/argocd-notifications-controller    1/1     1            1           78m
deployment.apps/argocd-dex-server                  1/1     1            1           78m
deployment.apps/argocd-repo-server                 1/1     1            1           78m
deployment.apps/argocd-server                      1/1     1            1           78m

NAME                                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/argocd-redis-69f8795dbd                       1         1         1       78m
replicaset.apps/argocd-applicationset-controller-596997cb6d   1         1         1       78m
replicaset.apps/argocd-notifications-controller-5c4c876d56    1         1         1       78m
replicaset.apps/argocd-dex-server-c8cb9bd99                   1         1         1       78m
replicaset.apps/argocd-repo-server-c8f5bd5c5                  1         1         1       78m
replicaset.apps/argocd-server-6bdd44685d                      1         1         1       78m

NAME                                             READY   AGE
statefulset.apps/argocd-application-controller   1/1     78m
```

And here you can see, everything has the status "running". You're ready to use Argo CD.

### Running the Argo CD web UI

In order to be able to use the command line interface and the user interface for Argo CD, we need to expose the Argo CD API Server outside of the cluster. 

So I'm going to patch the service that Argo CD has up and running on our cluster to expose its port and IP address outside of the cluster. 

So I'm going to patch it to be of type **node port**, the type node port for that service will expose that service to external connections coming from outside of the cluster. 

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
service/argocd-server patched
```

The service will be available at the IP address of the specific node and the corresponding port. 
```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl -n argocd get services                                               
NAME                                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
...
argocd-server                             NodePort    10.43.231.39    <none>        80:30650/TCP,443:32261/TCP   89m

```

So our Argo CD server now has IP type node port.



In order to access the service for the Argo CD, the API server, I'm going to use port forwarding. 

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl port-forward svc/argocd-server -n argocd 8080:443
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
```

I'm going to expose this service onboard 80 80. 

Once port forwarding has been set up, we should be able to access the Argo CD user interface at `localhost:8080` 

Get secret and enter `admin` and secret for the `password` field

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" |
 base64 -d && echo
j1ijYUJ3fzlNL5C-
```

### Setting up a Git repo with manifest specifications

The next step is to set up a git repository, where we'll store our Kubernetes manifest as well as our our application code. I already have git installed on my local machine. The git version that I have right now is 2.23.0. Let's head over to GitHub and I'm going to create a new public repository. We'll initially work with a public repository, and then we'll see how we can work with a private repository. Click on "Create Repository", and the name of my repository is "looney-argocd-public-repo". You can accept all of the default settings here. Just go to "Create Repository", and a new repository will be created. I now copy over the URL for this repository. We'll use it to clone this on our local machine. Run git in it first to make sure git is initialized. And once that's done, let's use "git clone" to clone the repository that we've just created. This repository is currently empty, but we'll soon fix that by adding the files required for our deployment. "ls -l" shows us that the folder for that repo is now present on my local machine. Let's cd into that folder and get started. The first application that we'll deploy using Argo CD is going to be a very simple nginx server. Now we'll specify the Kubernetes infrastructure using yaml_files. So create a sub folder called "nginx_yaml_files" and cd into that. And let's specify the manifest details for our deployment in "deployment.yaml". Here is our deployment. You can see that it's very straightforward. It's an nginx application. This deployment simply pulls in the default nginx image from Docker and exposes it as a service. We'll run this deployment with just one replica. So they'll be exactly one port running our nginx server. Hit Control + X and yes to save this file. And then let's set up our service dot yaml exposing our nginx server as a service. This is a very straightforward service deployed with the default namespace of our cluster. You can see that this service is exposed on both 80 and the IP address type is NodePort, which means that this service is accessible from outside of the cluster. It's not an internal service. Go ahead and save this service dot yaml file as well. We've kept our Kubernetes deployment very simple. We just have these two yaml specifications, one for the service and one for the deployment. I'll go up one folder level so that I'm in the looney Argo CD public repo folder. And here I'm going to add all of the new files that I have specified. Git add dot will add both the deployment dot yaml and the service dot yaml files, which are in the nginx yaml file sub folder. Just to note here, observe that these files are the declarative specification of the infrastructure that I want to use to run my application. My application is a simple nginx web server. I'm going to commit these manifest files to git and git will be used as the source of truth, when we set up continuous delivery using Argo CD. Run git push to push these changes to the master branch of our GitHub repository. We need to explicitly specify a username and password here because we haven't configured SSH to access this repo. Once all of the changes have been successfully pushed to the master branch, we can confirm this by heading over to our GitHub repo. And if you hit the refresh here on this page, you should find the files that we've just committed to the master. Nginx yaml files is a sub folder, and within it, you should find deployment dot yaml, and service dot yaml. We need to configure Argo CD to connect to this repository, to use as a source of truth. Click on code here and copy over the link to this repository. We'll head over to the Argo CD user interface that we have running and within the repository is projects and settings page and click on the repositories option. This is where you configure the repositories that Argo CD can connect to. Click on connect repo using HTTPS because that's the option that we've chosen. The repository type is git, and the repository URL is the URL that we just copied. Now, because this is a public repo, you do not need to specify a username and password. Click on the connect button that you see here on top. We'll accept all of the other default settings and the connection to your GitHub repo should be successful.

Connect to repo using HTTPS using ArgCD UI and point to this repo: https://github.com/vichuerta/kubernetes-gitops-with-argocd.git

`Settings > Repositories > Connect Repo > Via HTTPS > Repository URL : https://github.com/vichuerta/kubernetes-gitops-with-argocd.git > Connect`

### Deploying an application using Argo CD

On the Argo CD web UI, go back to the page that says, "Manage your applications". We'll create a new Argo CD application that will allow us to deploy our application from GitHub. Click on the "New App" button here. And this will take you to a webpage where you set up the details for this application. We'll call our application "simple-nginx-server", because that's what it is. And the Argo CD project that we'll use for this application is the default project. We'll see how to create a new project and use that in a demo that follows. Accept the default options for all of the other settings. The sync policy here is manual, indicating that we'll manually sync any changes we make to our GitHub repo to our application. Scroll down below, and there is a space where you specify the repository URL. This is a repository that has been already connected to Argo CD and should be available in the dropdown options. The source of truth for our infrastructure specifications is whatever manifest definition is present at the head of this repo. We need to specify a sub-part within this repo, and that is the "nginx_yaml_files" folder. The application and the infrastructure that you're about to deploy is going to be in the same Kubernetes cluster, where we have Argo CD running. In this case, the destination that you specify for the application deployment is the local URL of the cluster, which is Kubernetes dot default dot service. And within the local cluster, we'll deploy our application to the default namespace. Remember Argo CD services are running in the Argo CD namespace. We can accept the default values for all of the other settings, scroll down to the bottom, and then click on the create button. This will set up a new Argo CD application for you. If you remember, we had specified that this application will be synced manually, which means when you first set up this application, it will be out of sync. So the current state of this application in the Kubernetes cluster does not match the desired state, which is present in git. You can see from this diagrammatic overview here that Argo CD has connected to our GitHub repo and picked up the exact specification of the infrastructure that we want to deploy on Kubernetes. Our simple nginx server contains a single service object, the "nginx-service" and a single deployment object, "nginx-deployment". When your application status says, "OutOfSync", you can click on "App Diff" to see what the current state of the running application is on Kubernetes and how it differs from the state specified in git, which is our source of truth. From this diff, you can see that git contains the specification of our application, which we want deployed on Kubernetes, but there is no application running on our Kubernetes cluster yet. We're now ready to deploy our application, using Argo CD. Click on "Sync" and Argo CD will synchronize the state of truth that is present in git with the state that we have in our Kubernetes cluster. It will go ahead and deploy our application, and match the nginx service and nginx deployment specifications that we have in our git repo. Click on, "Synchronize" and wait for a few seconds for your application to be synced. The application that we have deployed is fairly simple, and that's why it takes just a few seconds. A more complex deployment with many more services will take longer. Our app health is healthy and our status is synced. So the state in git matches the state of the infrastructure deployment on our Kubernetes cluster. So they are in sync. You can see that our nginx service and deployment is run on one port, be it specified replica is equal to one. The same state of the application can be viewed directly in our cluster. Do a "get all" in the before namespace. And you should see all of the services and deployments and replicas associated with our simple nginx server. Our nginx web server is running on port 80. So I'm going to set up port forwarding so that on port 8081, we can access our nginx service. I'm going to head back to my web browser and open up a new tab and hit "localhost:8081". And there you see it, our welcome to nginx web page. This is the basic nginx server that we've successfully deployed using Argo CD. This completes our first successful deployment. Go ahead and kill the port forwarding for this particular service. Before we move on to the next demo, make sure you leave the port forwarding on for the Argo CD user interface. We'll be needing that.

Add new application on ArgoCD UI:

`Applications > NEW APP > GENERAL > Application Name: simple-nginx-server > Project Name: default > SYNC POLICY > Manual > SOURCE > Repository URL: https://github.com/vichuerta/kubernetes-gitops-with-argocd.git > Revision: HEAD > Path: nginx_yaml_files > DESTINATION > Cluster URL: https://kubernetes.default.svc > Namespace: default > DIRECTORY (leave default values)`

This application will show as `OutOfSync` by default you can use `APP DIFF` to view the drift of the application. 

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl -n default get all
NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-85d7bd697f-m7k58   1/1     Running   0          63s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/kubernetes      ClusterIP   10.43.0.1       <none>        443/TCP        3h16m
service/nginx-service   NodePort    10.43.207.147   <none>        80:30829/TCP   63s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   1/1     1            1           63s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-85d7bd697f   1         1         1       63s
```

you should see all of the services and deployments and replicas associated with our simple nginx server.


Our nginx web server is running on port 80. So I'm going to set up port forwarding so that on port 8081, we can access our nginx service.

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl port-forward svc/nginx-service -n default 8081:80
Forwarding from 127.0.0.1:8081 -> 80
Forwarding from [::1]:8081 -> 80
```

### Chapter Quiz

#### Question 1 of 4

When you first install Argo CD services on a Kubernetes cluster why does the "argocd version" command display an error?

- By default the Argo CD API Server running on the cluster is not exposed with an external IP address.
    
    Correct ✅
    
    The IP address type is `ClusterIP` by default, which means it is only visible internal to the cluster.
    
- By default the Argo CD services are not running and have to be explicitly run.
    
- By default argocd CLI commands require a special password before they can be executed.
    
- By default the Argo CD services do not allow access to the argocd command-line utility, special permissions have to be enabled to allow this.

#### Question 2 of 4

How do you get the password to log into the Argo CD web user interface?

- The default admin password is stored as a Kubernetes secret on the cluster where you are running Argo CD.
    
    Correct ✅
    
    You use kubectl to access the secret `argocd-initial-admin-secret` to get the default password.
    
- The default admin password is always the empty string.
    
- You need to explicitly update the password using the CLI before you can access the web user interface.
    
- The default admin password is always "password".


#### Question 3 of 4

In order to deploy your infrastructure and application on the same cluster where you have Argo CD services running what cluster URL would you configure on your Argo CD application?

- `https://kubernetes.default.svc`
    
    Correct ✅
    
    This is the URL to access the Kubernetes cluster on which Argo CD is deployed.
    
- `https://local.kubernetes.svc`
    
- `https://localhost/`
    
- `https://127.0.0.1`

#### Question 4 of 4

What is the status message that Argo CD displays when it finds that the live state of your deployment does not match the desired state stored in the Git repository?

- SyncRequired
    
- Unmatched
    
- NotMatching
    
- OutOfSync
    
    Correct ✅
    
    The live state is said to be out of sync with the desired state of the application if the live state does not match the state specified in Git.


## 3. Configuring the Argo CD Application

### Changing the Argo CD admin password

When we installed Argo CD on our locally running Kubernetes cluster, we saw that Argo CD was configured with a default admin user and we access the password for that admin user using git "secret". This gives us the password for the admin user that Argo CD has set up. 

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" |
 base64 -d && echo
j1ijYUJ3fzlNL5C-
```

What if you want to change this password? We can do this using the Argo CD command line interface, and you can login to the command line by finding the "argocd login localhost:8080" command. When you run this command, go ahead and select, "yes." We want to accept the Argo CD certificate and specify the username and password for the admin user. Once you've logged in successfully as an administrator, you can go ahead and run the command to change and update your password. That's simply "argocd account update-password". You'll be prompted to enter the current password. We've already copied that. So I'm simply going to paste that in and then you'll be prompted for the new password. Make sure you specify a password that you will remember because this will be the password that you use to log into Argo CD from here on in. Once you've updated your password, let's head back to the Argo CD web interface and click on log out. This will take us back to the username, password screen. Specify admin as the username and the new password that you just set. Go ahead and sign in and you should be logged in successfully and you can see our simple internet server is still running. It's still in the same state.

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % argocd account update-password
*** Enter password of currently logged in user (admin): 
*** Enter new password for user admin: 
*** Confirm new password for user admin: 
Password updated
Context 'localhost:8080' updated

new pw : j1ijYUJ3fzlNL5C_
```

### Syncing updated infrastructure manifests

We'll now see how we can make changes to our Kubernetes manifest, that we stored on GitHub, and have ArgoCD sync these changes for us. Let's see how many replicas of our deployment we're running, that's exactly one. 

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl -n default get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-85d7bd697f   1         1         1       26m
```

The current state matches the desired state. 

I'm now going to modify the deployment.yaml file, that I have on my local machine, to update the number of replicas that we have for our deployment. So open up deployment.yaml, and change the number of replicas. I've set it to be equal to two. Go ahead and save these changes. These changes are now made in the Git repository on our local machine. Let's now commit them to the master branch in GitHub. There's exactly one file that I've changed, deployment.yaml, which I add to Git, and I'm going to use git commit to commit the changes to my local repository. And once changes have been committed, I'm going to use git push to push these changes to the master branch in GitHub. Once changes have been pushed to the master branch of our repo, what this means is that the source of truth, that we have in GitHub, is now different from the state of our deployment in our Kubernetes cluster. The source of truth, here on GitHub, says that the number of replicas for our deployment should be equal to two, but, if you remember our application, our original deployment contained just one replica and this is why our ArgoCD application says OutOfSync. This OutOfSync message is a very clear indication that the infrastructure specification, in our connected repository, does not match the live deployment, and this is something that you might need to fix. Observe that ArgoCD has, very precisely, identified where exactly the mismatch lies between your desired state and the current state, it's in the nginx deployment. We've changed the number of replicas in this deployment. Let's take a look at the APP DIFF that will tell us the difference would be in the current state of the deployment and the desired state. Click on Compact diff to see a more compact representation, and you can see that the difference, here, lies in the number of replicas. We are now ready to use ArgoCD to sync up our application, so that the state of the application in GitHub matches the live state of the app. So you want to sync both the nginx service as well as the nginx deployment. Click on the Synchronize button, and ArgoCD will take care of the deployment for you. ArgoCD will make sure that the changes that you need to your manifest is reflected in the Kubernetes cluster. The change is in progress, and, in a few seconds, it'll change to healthy. And here you can see that the nginx deployment now contains two pods, which are replicas. Let's confirm that the number of replicas, for our nginx deployment, has been updated by getting the replica set in the default name space. And you can see here, nginx deployment is now at two replicas. That was the updated state based on our manifest.


### Configuring deployments using kubectl

Let's now see how Argo CD deals with the situation where you directly change the infrastructure that you've deployed for application using kubectl. Here, I use kubectl to change the number of replicas that I have for my nginx deployment to three. The Kubernetes manifests that I've checked into GitHub. Our source of truth states that our nginx deployment should have just two replicas. But now when you run "kubectl get all" you'll see that we have an additional replica. This is the replica that we've created directly on our Kubernetes cluster. So we use kubectl to set up this additional replica, which means when we go back to our Argo CD application, it tells us that our live state is not in sync with the source of truth, GitHub. Argo CD is very clear that it is a manifest specification corresponding to our nginx deployment that is out of sync. Our server specification is in sync. The deployment is out of sync. We have an additional replica. Click on "diff" here and you'll see the difference between the live manifest and the desired manifest. GitHub says the number of replica should be two, but our live manifest has three replicas. Let's say you want to ensure that your live deployment matches your GitHub status. Simply click on "sync" and Argo CD will get rid of the additional replica to make sure that your desired deployment state matches with the source of truth. That is GitHub. You can see that that extra replica that we have here is now in the terminating state. It'll be turned down and we'll be left with just two replicas for our nginx deployment. In under a minute, that extra replica that we had set up using kubectl will be terminated and our application state will go back to having two replicas for our deployment, matching our manifest specifications on GitHub.

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl -n default get replicaset             
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-85d7bd697f   2         2         2       31m
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl scale deploy nginx-deployment --replicas 3
deployment.apps/nginx-deployment scaled
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl get all                  
NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-85d7bd697f-m7k58   1/1     Running   0          32m
pod/nginx-deployment-85d7bd697f-l7s7n   1/1     Running   0          2m50s
pod/nginx-deployment-85d7bd697f-h4qq8   1/1     Running   0          56s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/kubernetes      ClusterIP   10.43.0.1       <none>        443/TCP        3h47m
service/nginx-service   NodePort    10.43.207.147   <none>        80:30829/TCP   32m

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   3/3     3            3           32m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-85d7bd697f   3         3         3       32m
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl -n default get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-85d7bd697f   3         3         3       32m
```

### Automated sync, automated pruning, and self-healing

So far, we've been manually syncing our Argo CD applications so that the live state of our deployment matches the state that we have specified in GitHub. But this is something that we can do automatically. Let's head over to app details. App details allows us to view all of the configuration settings for our Argo CD application. So here are the configuration settings that we set up when we first created this application. We are now going to change this a bit. If you scroll down below, you should find a section called sync policy. Let's go ahead and enable automatic sync. With this sync policy enabled Argo CD will automatically sync an application when it detects differences between the desired manifest that we've stored in Git and the live state of the deployment in the cluster. This means that your CICD pipelines need not access the Argo CD API server to manually think the application instead, simply changing the manifest on GitHub will be sufficient. **By default and as a safety mechanism, automated sync will not delete resources when Argo CD detects that the resource is no longer defined in Git. If you want automated sync, to be able to delete resources, you need to enable pruning of resources.** And that's what I have done here. **Another default specification of automated sync is that changes that are made to the live cluster will not trigger automated sync. So if you manually use kubectl to update your application, it's possible that your live deployment will be out of sync with what you have in Git. If you want to avoid this, you need to enable self-healing. With this setting also enabled the live state of our deployment will always match what we have in Git.** 

Well, with these changes made, let's take a look at our application that's currently running and has two replicas corresponding to our engineer's deployment. Let's change this. In our terminal window I'm going to run kubectl scale and change the number of replicas to be equal to 3. Remember that self-heal forces the live state of the deployment to always match what we have in Git. So if you run, get all, you'll see that the third replica that was created is already in the terminating state. Essentially the Argo CD controller saw that we had changed the live state of our application manually. Thanks to the self healing configuration setting that we had enabled. The live state is now synced back to the state that we have in GitHub where number of replicas is equal to 2. Now let's make a change to our infrastructure manifest specification on GitHub. On my terminal window I'm going to edit the deployment.yaml file. We had set number of replicas to 2 I have now switched it back to 1. Save these changes on your local machine. And these are the changes that we commit to the head version of our GitHub repository. So go ahead and use Git add to add deployment.yaml and Git commit to commit these changes on our local machine. With this done, Git push will now push these changes to the master branch in GitHub. As soon as these changes are pushed to GitHub Argo CD will receive a web hook event indicating that manifests in Git have changed. If you hit refresh, you'll see that our automated sync has now kicked into action. Our application is currently out of sync, but Argo CD has automatically started the syncing process. Because we've enabled automated pruning along with enabling automated sync, any resources that have been deleted in GitHub will be deleted by Argo CD. So here is our extra replica, which is now in the terminating state. And in a matter of seconds, this will be terminated. Before we move on to the next set of demos, let's clean up after assess. I'm going to delete this Argo CD application click on the delete button and specify the name of the application that you want to delete. This is just a confirm delete dialogue to ensure that you don't inadvertently delete something that you want to keep. Wait for a few seconds and you will find that Argo CD slowly cleans up your infrastructure. Your pods will be turned down, your deployment and service will also be turned down. You can see that app health is now missing. Wait a little more, and your application will be deleted. And finally, we can start off on an empty slate when we move on to the next demo.

`Applications > APP DETAILS > SYNC POLICY > ENABLE AUTO-SYNC > ENABLE PRUNE RESOURCES > ENABLE SELF HEAL`

### Chapter 3 Quiz

#### Question 1 of 2

What does the setting auto-sync accomplish when you enable this for your Argo CD application?

- When auto-sync is enabled Argo CD will delete your deployment if it finds that it receives no traffic.
    
- When auto-sync is enabled Argo CD will show you a warning when your live state does not match the desired state.
    
- When auto-sync is enabled Argo CD will automatically update the live state of the application to match the desired state without manual intervention.
    
    Correct ✅
    
    auto-sync enables syncing of deployments without explicit intervention from the release engineers.
    
- When auto-sync is enabled Argo CD will not delete resources which are no longer specified in your manifests.


#### Question 2 of 2

Which setting in Argo CD allows it to delete resources when you remove resources from your manifest specifications?

- Enabling Prune Resources
    
    Correct ✅
    
    Argo CD does not delete resources when Prune Resources is disabled.
    
- Enabling Versioning Rollback
    
- Enabling Auto-Sync
    
- Enabling Delete Resources

## 4. Deploying Infrastructure to External Kubernetes Cluster

### Creating an external K3s cluster

And all of the demos that we've done so far, the Argo CD controller and Argo CD services were running on the same cluster when we deployed our final application along with its infrastructure.

Now this might not be what you want to do in production. You might want to deploy your final application to a different production cluster. And this is what we'll see how to do in this demo. The cluster that the container application and infrastructure deployment will be on our local machine. And we expose that cluster while at the IP address of our local machine. So I run this command that you see here on screen to get the IP address of my local machine. This of course will be different than your keys. Now copy over this IP address, we need this when we define our dev cluster. I'll define the conflict specifications of this dev cluster here in this dev cluster config.yaml file. Our dev cluster is going to be a separate cluster. Not that this is not a cluster that Argo CD is currently running. This is a new cluster. Observe that I've used the TLS San flag to pass in a new subject alternate name. This basically tells Kubernetes to generate a certificate associated with my external IP address so that I can access the cluster using this external IP. Save this configuration file and we'll use the T3 D utility that we've installed earlier to create a new cluster based on the specifications in this config. This new cluster is going to be called the loony dev cluster. This is the cluster to which we deploy our application, Argo CD. We continue running on the previous cluster that we have created. Now, in order to work with this external cluster, Argo CD needs to access this cluster by IP address. The control plane of this new cluster that we set up is currently at 0.0.0.0, and the port 51766. Argo CD cannot access this cluster using this local host IP address. If you open up the cube config file on our local machine, you'll see that we now have the configuration settings for the two clusters that we are running, the original key 3D Argo CD cluster and the new key 3D loony dev cluster. For deployment to an external cluster outside of that Argo CD's running, we need access to an external IP address of that cluster. So I'm going to update you configure to specify the external address of my local machine. Thanks to the DLS fan argument that we had specified by setting up this cluster, this loony dev cluster should be accessible via this external IP and the port 51766. Go ahead and save the file with these changes. Our cube control command is currently managing the loony dev cluster. Run cube control version to make sure that the cluster is still accessible to cube control with the right external IP and our certificate set up correctly. This should work and give no errors.

```bash
# get IP address of my local machine
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % IP=`ifconfig en0 | grep inet | grep -v inet6 | awk '{print $2}'` && echo $IP
192.168.1.9
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % k3d cluster create looney-dev-cluster --config dev-cluster-config.yaml 
INFO[0000] Using config file dev-cluster-config.yaml (k3d.io/v1alpha2#simple) 
WARN[0000] Default config apiVersion is 'k3d.io/v1alpha5', but you're using 'k3d.io/v1alpha2': consider migrating. 
INFO[0000] Prep: Network                                
INFO[0000] Created network 'k3d-looney-dev-cluster'     
INFO[0000] Created image volume k3d-looney-dev-cluster-images 
INFO[0000] Starting new tools node...                   
INFO[0000] Starting Node 'k3d-looney-dev-cluster-tools' 
INFO[0001] Creating node 'k3d-looney-dev-cluster-server-0' 
INFO[0001] Creating LoadBalancer 'k3d-looney-dev-cluster-serverlb' 
INFO[0001] Using the k3d-tools node to gather environment information 
INFO[0001] Starting new tools node...                   
INFO[0002] Starting Node 'k3d-looney-dev-cluster-tools' 
INFO[0003] Starting cluster 'looney-dev-cluster'        
INFO[0003] Starting servers...                          
INFO[0003] Starting Node 'k3d-looney-dev-cluster-server-0' 
INFO[0006] All agents already running.                  
INFO[0006] Starting helpers...                          
INFO[0006] Starting Node 'k3d-looney-dev-cluster-serverlb' 
INFO[0013] Injecting records for hostAliases (incl. host.k3d.internal) and for 3 network members into CoreDNS configmap... 
INFO[0015] Cluster 'looney-dev-cluster' created successfully! 
INFO[0015] You can now use it like this:                
kubectl cluster-info
```


```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl cluster-info
Kubernetes control plane is running at https://0.0.0.0:55162
CoreDNS is running at https://0.0.0.0:55162/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://0.0.0.0:55162/api/v1/namespaces/kube-system/services/https:metrics-server:https/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % cat ~/.kube/config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: <CERT>
    server: https://0.0.0.0:61672
  name: k3d-argocd-cluster
- cluster:
    certificate-authority-data: <CERT>
    server: https://192.168.1.9:55162 # external IP of my local cluster, thanks to extraServerArgs: --tls-san=192.168.1.9 in cluster config yaml 
  name: k3d-looney-dev-cluster
```

For deployment to an external cluster outside of that Argo CD's running, we need access to an external IP address of that cluster. So I'm going to update you configure to specify the external address of my local machine. Thanks to the DLS fan argument that we had specified by setting up this cluster, this loony dev cluster should be accessible via this external IP and the port 51766.

Our cube control command is currently managing the loony dev cluster. Run cube control version to make sure that the cluster is still accessible to cube control with the right external IP and our certificate set up correctly. This should work and give no errors.

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl version
Client Version: v1.29.2
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: v1.27.5+k3s1
WARNING: version difference between client (1.29) and server (1.27) exceeds the supported minor version skew of +/-1
```

### Creating a new Argo CD project using the CLI

kubectl on our local machine currently can be used to manage both of our clusters. Call "get-contexts" and you should see the two clusters that we have installed, the argocd-cluster and the loony-dev-cluster. Let's switch over to configuring and managing the Argo CD cluster. So far, we've used the web UI to manage and configure Argo CD. Argo CD can be used from the command line as well. So you can use these commands within your scripts. Here, I'm adding the loony-dev-cluster as an external cluster to Argo CD. This allows Argo CD to learn of the existence of this cluster, and it will be able to deploy applications to this cluster. Let's view the projects that we have available on Argo CD. For that we need to log in to the Argo CD command line interface. "admin" is the username. Specify the new password that you have set for the admin login. And once you're logged in, you can view the current list of Argo CD projects by running "argocd proj list". Projects on Argo CD allow you to logically group your applications together. This is especially useful if multiple teams within your organization is running Argo CD. So each team can have its own project and all of their applications will live within that project. By default, we have just one project on Argo CD. That is the default project. If you want to view the list of repositories that Argo CD is currently connected to, "argocd repo list" will give you that information. There is exactly one repository, the public repo that we connected Argo CD to in an earlier demo. If you want to view the current list of your Argo CD applications, "argocd app list" will give you this information. Since we've deleted all of our applications, there are no apps listed here. I'm now going to open up my kube.config file in order to access the IP address and port at which my control plane for the dev cluster is running. I'm going to copy this over and use this in the next command. This time around, I want to create a new Argo CD application within a new project. I'm using the command line here to create a new project called "loony-argocd". This new project will provision Kubernetes infrastructure and deploy applications to an external cluster specified by this IP address, our external IP and the port 51766. Applications will be deployed to the default namespace on this external cluster. I've also connected up the public repository that we created earlier with this project. Once this project has been created, you can run "argocd proj list" and this should show you that in addition to the default project, which is created by default, we also have this new project, the loony-argocd project. I still have the Argo CD user interface running. I've deleted the old application. Head over to where you manage your projects and your clusters. Click on "Projects" and you should see that we have our new project here, the loony-argocd project that we've created via the command line. Observe that this project is configured with our public GitHub repo, the loony-argocd-public-repo, and if you scroll further down, you'll see that this project will deploy apps to our external K3S cluster. We can verify this connection to the external cluster by heading back to manage repositories clusters. And on this page, click through to the "Clusters" link that you see here. This page shows you all of the clusters that Argo CD can deploy to. "in-cluster" is the cluster on which Argo CD is running. "dev-cluster" is our external cluster, which we've successfully connected up to Argo CD. Now there are no applications running on this cluster. That's why the status of the cluster is not known.

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl config get-contexts -o name
k3d-argocd-cluster
k3d-looney-dev-cluster
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % kubectl config use-context k3d-argocd-cluster               
Switched to context "k3d-argocd-cluster".
```

```bash
# adding looney dev cluster as an external cluster to ArgoCD this allows Argo CD to learn of the existence of this cluster, and it will be able to deploy applications to this cluster.
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % argocd cluster add k3d-looney-dev-cluster --name dev-cluster
WARNING: This will create a service account `argocd-manager` on the cluster referenced by context `k3d-looney-dev-cluster` with full cluster level privileges. Do you want to continue [y/N]? y
INFO[0001] ServiceAccount "argocd-manager" already exists in namespace "kube-system" 
INFO[0001] ClusterRole "argocd-manager-role" updated    
INFO[0001] ClusterRoleBinding "argocd-manager-role-binding" updated 
Cluster 'https://192.168.1.9:55162' added
```

```bash
# view the current list of Argo CD projects. Projects on Argo CD allow you to logically group your applications together. This is especially useful if multiple teams within your organization is running Argo CD. So each team can have its own project and all of their applications will live within that project. By default, we have just one project on Argo CD. That is the default project.
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % argocd proj list
NAME     DESCRIPTION  DESTINATIONS  SOURCES  CLUSTER-RESOURCE-WHITELIST  NAMESPACE-RESOURCE-BLACKLIST  SIGNATURE-KEYS  ORPHANED-RESOURCES
default               *,*           *        */*                         <none>                        <none>          disabled
```

```bash
# view the list of repositories that Argo CD is currently connected
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % argocd repo list
TYPE  NAME  REPO                                                            INSECURE  OCI    LFS    CREDS  STATUS      MESSAGE  PROJECT
git         https://github.com/vichuerta/kubernetes-gitops-with-argocd.git  false     false  false  false  Successful           
```

```bash
# view the current list of your Argo CD applications
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % argocd app list
NAME                        CLUSTER                         NAMESPACE  PROJECT  STATUS  HEALTH   SYNCPOLICY  CONDITIONS  REPO                                                            PATH              TARGET
argocd/simple-nginx-server  https://kubernetes.default.svc  default    default  Synced  Healthy  Auto-Prune  <none>      https://github.com/vichuerta/kubernetes-gitops-with-argocd.git  nginx_yaml_files  course
```

Referencing the IP and Port on which my Control Plane for the dev cluster is running I can use it to create a new ArgoCD project

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % argocd proj create looney-argocd -d https://192.168.1.9:55162,default -s https://github.com/vichuerta/kubernetes-gitops-with-argocd.git
```

```bash
victorhuerta@Victors-MacBook-Pro kubernetes-gitops-with-argocd % argocd proj list   
NAME           DESCRIPTION  DESTINATIONS                       SOURCES                                                         CLUSTER-RESOURCE-WHITELIST  NAMESPACE-RESOURCE-BLACKLIST  SIGNATURE-KEYS  ORPHANED-RESOURCES
default                     *,*                                *                                                               */*                         <none>                        <none>          disabled
looney-argocd               https://192.168.1.9:55162,default  https://github.com/vichuerta/kubernetes-gitops-with-argocd.git  <none>                      <none>                        <none>          disabled
```


### Building and registering a custom Docker image

Here we are on GitHub. We'll create a new private repository. And we'll see how we can have Argo CD connect to this private repository to access our infrastructure as code specifications. This repository will be called loony-argocd-private-repo. And make sure you select the Private option here. We'll keep the remaining settings as is and go ahead and create this repository. Now, accessing a private repo will require us to specify credentials for this repo. Copy over the URL for this repo. And on our local machine within our terminal window, I'm going to run git clone in order to clone this repository onto my local machine. This time around, we'll build our own custom Python application, a very simple one, which we'll register with Docker Hub. And this is the application that we deploy to our Kubernetes cluster. Create a subdirectory called hello_app and CD into it. Our GitHub repo will contain the code for our application here in this app subfolder. It will also contain the infrastructure specifications of our deployment. Let's take a look at our application code. Let's write some simple code in main.py. This is a simply Python Flask web server, which has just one route configured. The home route will simply return Hello World, deployed using Argo CD. In order to keep things simple, we'll simply run this Flask application on the development server. Go ahead and save these changes. Next, we'll specify the dependencies of this application in requirements.txt. The only thing that this app depends on is the Flask Library. So simply specify Flask here and save the changes to this file. Now, I'm going to have my application run within a Docker container. I'm going to set up a Docker file, which specifies how the image for my application is to be built. We'll use Python 3.7. We'll run pip install on requirements.txt. And our web application will be exposed on port 5,000. Now that we've set up the code for our very simple web app, I'm going to use Docker build to build an image for this hello_app. This image is V one, version one. Clouduserloony is my username on Docker Hub, which is where I'll be registering this image. Go ahead and build this image. So this image is now available on our local machine. And we can register this image on Docker Hub. Docker image LS should show you that this image has been successfully created. On the web browser, head over to hub.docker.com. You already need to have a Docker account created. Make sure you log into that account. I'm now clicking on Repositories in order to create a new repository to hold my image. I'll call this repository hello-app because that corresponds to the name of my application. Let's make this repository a private one because any custom application that you register with Docker Hub is likely to be in a private repo. Click on Create. And this repository will be created for you. Back to a terminal window where we'll log into Docker using Docker login. You need to specify the username and password of your Docker account. We'll push the custom image that we've built for our application and register that image with Docker Hub. Now, this will normally be done by a CI/CD pipeline for your code. Since our focus here is on continuous delivery of our infrastructure using Argo CD, I'm going to manually run Docker push to push V one off this hello-app that we've created. And register this image with Docker Hub. You can head over to Docker Hub and hit refresh. And you should find version one of our image here. Scroll down a little bit here. And here you can see V one of our app successfully registered with Docker Hub. The image for our custom application is now registered in a private repo on Docker Hub. We need to configure a secret on our Kubernetes cluster where the application will be deployed so that our deployment infrastructure can pull this custom image from Docker Hub. And that's what we'll do next. Remember, I have two clusters running on my local machine, two key 3S clusters. Get context will give me the names of both of these clusters. So I have the Argo CD cluster where Argo CD services are running and the loony-dev-cluster where we are going to deploy our app. I'm going to switch Kube control to use the loony-dev-cluster context so I can book on that cluster. This dev cluster where our application is to be deployed is where we'll create the secret that will allow our deployment to access our custom image on Docker Hub. Get secret shows that there's exactly one secret present in this cluster at this point in time. We'll create and register a new Kubernetes secret that will allow our deployment to access our custom image. Let's take a look at the config.JSON file for Docker. Observe the URL for Docker index.docker.io/v1. Keep this in mind and close this file. And let's create a new secret using Kube control create secret. The name of the secret will be called regcred. This is where we'll use our Docker credentials to create a new secret that our deployment can access. our Kubernetes deployment will use this secret to pull the custom image for our Python Flask application to be deployed on Kubernetes. Kube control get secret will show you that the secret with the name regcred has been successfully created. This is the secret for the Docker registry where our image lives.


### Pushing infrastructure specifications to a private Git repo

We built and registered the image for our application's code. Let's now turn our attention towards our deployment, our infra as code specification. Within the hello app subfolder, create a new deployment.yaml file. This is the deployment for our Python flask app. I've used an Argo CD annotation to indicate that the external link for this deployment is the image on hub.docker.com. You can see that we are running this application with one replica. And the image that our application needs to deploy is the hello-app:v1 image. And in order to access this image in our private repo, it should use the regcred secret that we've created. Let's save the file for our deployment specification and open up service.yaml, where we'll configure this service for our application. "hello-app-service" is the name of the service. The port on which the service will be exposed within the cluster is 6000. The target port to which we'll send our request will be 5000. And it is of type "NodePort", which implies that external clients can send traffic to that port. It's not an internal port. Save this file. And let's now add all of the files that we've created to git. This includes the files for our Python flask application, and also the files that contain our infrastructure deployment. Here deployment and service yaml specifications are our infrastructure manifests. I use the "git commit" command to commit these files to my local machine. And once that's done, these files are now ready to be pushed to the master branch of our repository. Remember, this is the private repository, loony-argocd-private-repo.


### Deploying a Flask application to an external cluster

My Argo CD web UI is still up and running. I'm going to click on this settings icon here to manage Repositories, Projects, and Settings. Let's click through to Repositories and connect the private repository where we've submitted our application and infrastructure code to get. Click on Connect Repo Using HTTPS. Specify the HTTPS path to our loony-argocd-private-repo. And because the repo happens to be private, you'll need to specify a username and password that Argo CD can use connect to this repository to look for changes in your infrastructure specification. Go ahead and click on Connect. You can use all of the other default settings specified. Once our Argo CD has successfully connected to our private repository, let's head back to our command line and use the Argo CD CLI interface to add this repository as a source to the loony-Argocd project that we've created. I'm trying to give you a little bit of taste of working with the Argo CD command line along with the user interface. Back to our user interface. Go to the main page where we manage our repositories and projects. Click on the projects link. And let's ensure that our loony-argocd-project can indeed connect to our private repository. Let's scroll down here to the bottom. Originally, this particular project could only access the public repository, but now, we've added the private repository as well. With loony-argocd-project completely set up, we are now ready to create a new application to manage our infrastructure for our Hello World Python Flask app. Click on this New App button. The name of the application within Argo CD is hello-app. And the project where we'll create this application is Loony-argocd. Let's scroll down a little further. The source of our infrastructure specification is in the loony-argocd-private repository. And we want the life state of our deployment to be synced from the head revision of this repo. Now, the part within this repo, which contains our deployment infrastructure is the hello_app part. Now, remember, that Argo CD is running on one cluster, but we want our application and resource deployment to be on an external cluster. So when you click on the dropdown for cluster, you'll find two options. Select the option for the external cluster. At the time of application configuration, you can always specify which cluster you want your app to be deployed to. And within this external, our loony-dev-cluster, we'll deploy to the default namespace. This Argo CD application that we've created is essentially an application that runs on our Argo CD cluster. So you can edit the application specification in the YAML form. The settings that we specified in the friendly_user interface maps to this YAML specification for our Argo CD app. Time for us to create this application. Because we accepted the default manual sync option, you'll find that initially, your application status says, Out of Sync. Clicking on the Sync button will cause Argo CD to start the sync process, matching the live state of our application with the state that is specified in GitHub. I'm going to sync both service as well as the deployment specifications. And Synchronize will do exactly that. You can sync just one component of your application at a time if you want to, but that's a much more advanced functionality that we won't get into now. You can see that our hello-application has been successfully deployed and is running on a single replica. If you find that your deployment results in an error, you might want to use logs in order to debug what's wrong. Click on the deployment. And you have a number of tabs giving you different bits of information. The Logs tab here will give you the log messages associated with your deployment. Here the log messages show that our Flask application is successfully running, but you can use these logs to debug errors. Back to our terminal window. And let's run Kube control get all. I'm on the loony-dev-cluster here. And here you can see our hello-app-service is running on port 6,000. This has been deployed on the external cluster. Let's set up port forwarding to forward 8081 to port 6,000. And we can now access our Python Flask application on port 8081. We just have the homepage route configured on our app. And that returns, "Hello World! Deployed using Argo CD." Next up, I'm going to make a few changes to our Python application and deploy those updates. So I'm going to kill this port forwarding that I've set up here and get back to editing code.

### Updating the custom application

Here I am on my terminal prompt within the "hello_app/app" subfolder. I'm now going to make a change to my custom Python application. In main.py, I'm going to add an additional app route. "/argocd" will return the message, "Isn't Argo CD pretty amazing?" Save the changes to main.py. Remember, these are code changes. These will be handled by our CICD pipeline for code. Here for the sake of simplicity, we'll just build a custom image. This is the V2 image using Docker build. Once the image has been built successfully, you can use "docker push" to push this updated version of your hello app image to Docker Hub. It'll register V2 with Docker Hub after the push command is complete. This completes the update that we wanted to make to our code. Let's now make some updates to our infrastructure specification as well. Open up deployment.yaml, which is in the hello_app folder and let's update our deployment to point to this new version of our application, which is contained in the image hello-app:v2. Having made changes to our code as well as our deployment, let's add the files that we've changed to git. So "git add main.py" and "deployment.yaml" And let's now commit these changes to our GitHub repo. This is our private repository. The "git push" command will then push these changes to the head revision of the master branch in GitHub. With this done, we can now head over to Argo CD and sync our application so that it gets the latest version. It picks up the new V2 image. Click on "sync" and then synchronize here and wait for a few seconds for the sync to be complete. Argo CD seems to indicate that our sync was successful. So let's head back to our terminal window and set up port forwarding so we can access this application on port 8081. Let's head over to a browser window and run "localhost 8081" and there you see it. Our default route says, "Hello World! Deployed using Argo CD." But this should be the updated application. Let's look at the "/argocd" route in this app. And this should give you, "Isn't Argo CD pretty amazing?" We've successfully updated our deployment using Argo CD.

### Chapter 4 Quiz

#### Question 1 of 1

Why would you choose to create and use a specific Project for your Argo CD application?

- Projects provide a logical grouping of applications which is useful when Argo CD applications are used by multiple teams.
    
    Correct ✅
    
    Projects also allow you to specify roles, limit which repositories you connect to, and what kind of applications you deploy.
    
- Projects allow you to set alerts to warn you if your application is not running.
    
- Projects help you secure access to your applications so once deployed specifications cannot change.
    
- Projects allow you to monitor deployed applications to ensure that they are running as expected.

