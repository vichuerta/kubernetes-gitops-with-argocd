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


