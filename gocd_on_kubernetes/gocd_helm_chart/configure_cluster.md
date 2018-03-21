---
description: The GoCD Helm Chart page explains how to get started with GoCD for kubernetes using Helm.
keywords: gocd helm chart, cd pipeline
---

# Configure your Kubernetes Cluster

Now that you have your Kubernetes cluster [set up and running](setup.md), you’ll need to make the following configuration changes.

**1. Enable Ingress** - to be able to expose the GoCD application over your network, you will need an [Ingress controller](https://kubernetes.io/docs/concepts/services-networking/ingress/#ingress-controllers).

**2. Configure Service Account** - A [service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) is how Kubernetes manages roles and permissions. The default Kubernetes service account needs to be associated with the cluster-admin role to be able to install the GoCD application.

This configuration will be specific to your target Kubernetes environment.

## Option 1: Configure minikube

#### Enable Ingress

You can enable Ingress on minikube with this command

```bash
$ minikube addons enable ingress
   ingress was successfully enabled
```

#### Configure service account

The `kubeadm` toolkit helps to easily bootstrap a cluster so that appropriate privileges are granted for performing read-write operations on Kubernetes authentication and authorization (RBAC) resources.
Add the bootstrapper flag to the minikube start command as shown in the [setup](setup.md#2-setup-a-kubernetes-cluster) step. This may initially take several minutes to download and setup.

## Option 2: Configure GKE

#### Enable Ingress
GKE comes with Ingress enabled by default, so no action needed here.

#### Configure service account
The `cluster-admin` role is a cluster level role that exists on GKE.

Associate the cluster role with the service account with a cluster role binding:
```bash
kubectl create clusterrolebinding clusterRoleBinding \
--clusterrole=cluster-admin \
--serviceaccount=kube-system:default
```

## Option 3: Configure kops

#### Enable Ingress

To enable ingress for kops, you can configure an ingress controller of your own or an [ingress addon](https://github.com/kubernetes/kops/tree/master/addons/ingress-nginx) provided by kops.

#### Configure service account

Kops requires a cluster role and cluster role binding to be set up for the service account associated with the `Tiller` pod.

To create the cluster admin role, apply the following resource descriptor:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-admin
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Associate the cluster role with the service account with a cluster role binding:
```bash
kubectl create clusterrolebinding clusterRoleBinding \
--clusterrole=cluster-admin \
--serviceaccount=kube-system:default
```
