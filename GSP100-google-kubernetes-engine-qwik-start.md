# Google Kubernetes Engine: Qwik Start

## GSP100

### Overview

Google Kubernetes Engine (GKE) provides a managed environment for deploying, managing, and scaling your containerized applications using Google infrastructure. 

The GKE environment consists of multiple machines (specifically Compute Engine instances) grouped to form a container cluster.

In this lab, you get hands-on practice with container creation and application deployment with GKE.

### Objectives

In this lab you will learn how to:

- Create a GKE cluster
- Deploy an application to the cluster
- Delete the cluster

#### Cluster orchestration with Google Kubernetes Engine

Google Kubernetes Engine (GKE) clusters are powered by the Kubernetes open source cluster management system. 

Kubernetes provides the mechanisms through which you interact with your container cluster. 

You use Kubernetes commands and resources to deploy and manage your applications, perform administrative tasks, set policies, and monitor the health of your deployed workloads.

Kubernetes draws on the same design principles that run popular Google services and provides the same benefits: automatic management, monitoring and liveness probes for application containers, automatic scaling, rolling updates, and more. 

When you run your applications on a container cluster, you're using technology based on Google's 10+ years of experience with running production workloads in containers.

#### Kubernetes on Google Cloud

When you run a GKE cluster, you also gain the benefit of advanced cluster management features that Google Cloud provides. 

These include:

- Load balancing for Compute Engine instances
- Node pools to designate subsets of nodes within a cluster for additional flexibility
- Automatic scaling of your cluster's node instance count
- Automatic upgrades for your cluster's node software
- Node auto-repair to maintain node health and availability
- Logging and Monitoring with Cloud Monitoring for visibility into your cluster

Now that you have a basic understanding of Kubernetes, you will learn how to deploy a containerized application with GKE in less than 30 minutes. 

### Task 1. Set a default compute zone

Your compute zone is an approximate regional location in which your clusters and their resources live. 

For example, `us-central1-a` is a zone in the `us-central1` region.

In your Cloud Shell session, run the following commands.

Set the default compute region:

```bash
gcloud config set compute/region us-east1
```

Expected output:

```bash
Updated property [compute/region].
```

Set the default compute zone:

```bash
gcloud config set compute/zone us-east1-c
```

Expected output:

```bash
Updated property [compute/zone].
```

### Task 2. Create a GKE cluster

A cluster consists of at least one **cluster master** machine and multiple worker machines called **nodes**. 

Nodes are Compute Engine virtual machine (VM) instances that run the Kubernetes processes necessary to make them part of the cluster.

> Note: Cluster names must start with a letter and end with an alphanumeric, and cannot be longer than 40 characters.

Run the following command:

Create a cluster:

```bash
gcloud container clusters create --machine-type=e2-medium --zone=us-east1-c lab-cluster
```

You can ignore any warnings in the output. It might take several minutes to finish creating the cluster.

Expected output:

```bash
NAME: lab-cluster
LOCATION: us-east1-c
MASTER_VERSION: 1.22.8-gke.202
MASTER_IP: 34.67.240.12
MACHINE_TYPE: e2-medium
NODE_VERSION: 1.22.8-gke.202
NUM_NODES: 3
STATUS: RUNNING
```

### Task 3. Get authentication credentials for the cluster

After creating your cluster, you need authentication credentials to interact with it.

Authenticate with the cluster:

```bash
gcloud container clusters get-credentials lab-cluster
```

Expected output:

```bash
Fetching cluster endpoint and auth data.
kubeconfig entry generated for my-cluster.
```

### Task 4. Deploy an application to the cluster

You can now deploy a containerized application to the cluster. 

For this lab, you'll run `hello-app` in your cluster.

GKE uses Kubernetes objects to create and manage your cluster's resources. 

Kubernetes provides the Deployment object for deploying stateless applications like web servers. 

Service objects define rules and load balancing for accessing your application from the internet.

To create a new Deployment hello-server from the hello-app container image, run the following kubectl create command:

```bash
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
```

Expected output:

```bash
deployment.apps/hello-server created
```

This Kubernetes command creates a deployment object that represents `hello-server`. 

In this case, `--image` specifies a container image to deploy. 

The command pulls the example image from a Container Registry bucket. 

`gcr.io/google-samples/hello-app:1.0` indicates the specific image version to pull. 

If a version is not specified, the latest version is used.

To create a Kubernetes Service, which is a Kubernetes resource that lets you expose your application to external traffic, run the following `kubectl expose` command:

```bash
kubectl expose deployment hello-server --type=LoadBalancer --port 8080
```

In this command:

- `--port` specifies the port that the container exposes.
- `type="LoadBalancer"` creates a Compute Engine load balancer for your container.

Expected output:

```bash
service/hello-server exposed
```

To inspect the `hello-server` Service, run `kubectl get`:

```bash
kubectl get service
```

```bash
Expected output:

NAME             TYPE            CLUSTER-IP      EXTERNAL-IP     PORT(S)           AGE
hello-server     loadBalancer    10.39.244.36    35.202.234.26   8080:31991/TCP    65s
kubernetes       ClusterIP       10.39.240.1               433/TCP           5m13s
```

> Note: It might take a minute for an external IP address to be generated. Run the previous command again if the `EXTERNAL-IP` column status is **pending**.

To view the application from your web browser, open a new tab and enter the following address, replacing `[EXTERNAL IP]` with the `EXTERNAL-IP` for `hello-server`.

```bash
http://[EXTERNAL-IP]:8080
```

Expected output: 

The browser tab displays the message **Hello, world!** as well as the version and hostname.

### Task 5. Deleting the cluster

To **delete** the cluster, run the following command:

```bash
gcloud container clusters delete lab-cluster
```

When prompted, type **Y** to confirm.

Deleting the cluster can take a few minutes. 

For more information on deleted GKE clusters from the Google Kubernetes Engine (GKE) article, Deleting a cluster.

### Congratulations

You have just deployed a containerized application to Google Kubernetes Engine! 

In this lab, you created a GKE cluster, deployed a containerized application to the cluster, and deleted the cluster. 

You can now apply this knowledge to deploy your own applications with GKE.
