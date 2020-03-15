# Deploying tradefinance app to GKE via Google Cloud Marketplace

## Overview
tradefinance is a application for document automation, i.e. extraction of entities from digital documents.

The 
[Google Cloud Marketplace][1] 
is a easy way to deploy apps like tradefinance to a 
[Google Kubernetes Engine][2] 
cluster, with just a few clicks.

[1]: https://console.cloud.google.com/
[2]: https://cloud.google.com/kubernetes-engine/
## Installation

### Quick install with Google Cloud Marketplace

Deploy tradefinance to Google Kubernetes Engine using Google Cloud Marketplace, by following the [on-screen instructions.]()

### Command line instructions

#### Prerequisites

##### Set up command-line tools

You'll need the following tools in your development environment. If you are using Cloud Shell, then gcloud, kubectl, Docker, and Git are installed in your environment by default.

* [gcloud](https://cloud.google.com/sdk/gcloud/)
* [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)
* [docker](https://docs.docker.com/install/)
* [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [helm](https://helm.sh/)

Configure gcloud as a Docker credential helper:

```bash
gcloud auth configure-docker
```

##### Create a Google Kubernetes Engine (GKE) cluster

Create a new cluster from the command line:

```bash
export CLUSTER=cert-manager-cluster
export ZONE=us-west1-a

gcloud container clusters create "${CLUSTER}" --zone "${ZONE}"
```

Configure ```bash kubectl``` to connect to the new cluster:
```bash 
gcloud container clusters get-credentials "${CLUSTER}" --zone "${ZONE}"
```

##### Clone this repo

Clone this repo and its associated tools repo:

```bash
git clone --recursive https://github.com/GoogleCloudPlatform/click-to-deploy.git
```

##### Install the Application resource definition

An Application resource is a collection of individual Kubernetes components, such as Services, Deployments, and so on, that you can manage as a group.

To set up your cluster to understand Application resources, run the following command:

```bash
kubectl apply -f "https://raw.githubusercontent.com/GoogleCloudPlatform/marketplace-k8s-app-tools/master/crd/app-crd.yaml"
```

You need to run this command once.

The Application resource is defined by the [Kubernetes SIG-apps](https://github.com/kubernetes/community/tree/master/sig-apps) community. The source code can be found on [github.com/kubernetes-sigs/application](https://github.com/kubernetes-sigs/application).

#### Install the app

Navigate to the ```bash cert-manager``` directory:

##### Configure the app with environment variables

Choose an instance name and [namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) for the app. In most cases, you can use the ```bash default``` namespace.

```bash 
export APP_INSTANCE_NAME=cert-manager-1
export NAMESPACE=default
```

Configure the container image:
```bash
export TAG=0.13
export IMAGE_CONTROLLER="marketplace.gcr.io/google/cert-manager"
export IMAGE_METRICS_EXPORTER="marketplace.gcr.io/google/cert-manager/prometheus-to-sd:${TAG}"
```
By default 1 replica for each deployment, but optionally you can set the number of replicas for Cert Manager controller, webhook and cainjector.

```bash
export CONTROLLER_REPLICAS=3
export WEBHOOK_REPLICAS=3
export CAINJECTOR_REPLICAS=3
```

##### Create namespace in your Kubernetes cluster

If you use a different namespace than the ```bash default```, run the command below to create a new namespace:

```bash
kubectl create namespace "${NAMESPACE}"
````
