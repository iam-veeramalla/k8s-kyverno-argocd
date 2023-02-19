# Enforce Automated k8s cluster security using kyverno policy generator and argocd

In this project we will learn how to enforce policies, governence and compliance on your kubernetes cluster. Whether your kubernetes cluster is on AWS, Azure, GCP or on-premises, this project will work without any additional changes.

To explain the project with examples, using this configuration you can 

1. Generate -> For example, Create a default network policy whenever a namespace is created.
2. Validate -> For example, Block users from using `latest` tag in the deployment or pod resources.
3. Mutate -> For example, Attach pod security policy for a pod that is created without any pod security policy configuration.
4. Verify Images -> For example, Verify if the Images used in the pod resources are properly signed and verified images.

## High Level Design

On a very high level, A DevOps Engineer will write the required Kyverno Policy custom resource and commits it to a Git repository. Argo CD which is pre configured with `auto-sync` to watch for resources in the git repo, deploys the Kyverno Policies on to the Kubernetes cluster.

![Screenshot 2023-02-19 at 12 40 48 PM](https://user-images.githubusercontent.com/43399466/219934201-b542599a-7f8a-4b72-a1bf-5db6ba1bfade.png)


## Installation

To setup this project you need to install Argo CD controller and Kyverno controller, Assuming you have Kubernetes installed.

Installation of both Kyverno and Argo CD are pretty straight forward as both of them support Helm charts and also provide a consolidated 
installation yaml files. 

### Kyverno

There are two easy ways to install kyverno:

1. Using Helm
2. Using the kubernetes manifest files

### Using Helm 

Add helm repo for kyverno 

```
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update
```

Install kyverno in HA mode

```
 helm install kyverno kyverno/kyverno -n kyverno --create-namespace --set replicaCount=3
```

(or)

Install kyverno in Standalone mode

```
helm install kyverno kyverno/kyverno -n kyverno --create-namespace --set replicaCount=1
```

Install a specific version of kyverno

```
helm search repo kyverno -l | head -n 10
```

```
helm install kyverno kyverno/kyverno -n kyverno --create-namespace --version 2.6.5
```

### Using Kubernetes manifest yaml files

```
kubectl create -f https://github.com/kyverno/kyverno/releases/download/v1.8.5/install.yaml
```

### Argo CD

There are three ways to install Argo CD

1. `kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-cd/master/manifests/install.yaml`
2. Helm Charts, Follow the [link](https://github.com/argoproj/argo-helm/tree/main/charts/argo-cd#installing-the-chart) 
3. Using the Argo CD Operator, Follow the [link](https://argocd-operator.readthedocs.io/en/latest/install/olm/)

## Demystifying Kyverno & Kyverno Policies 

Kyverno is a policy engine designed for Kubernetes

A Kyverno policy is a collection of rules. Each rule consists of a match declaration, an optional exclude declaration, and one of a validate, mutate, generate, or verifyImages declaration. Each rule can contain only a single validate, mutate, generate, or verifyImages child declaration.

![image](https://user-images.githubusercontent.com/43399466/219931973-14c0f501-ae49-4cab-9da5-b01950cc308f.png)

Policies can be defined as cluster-wide resources (using the kind ClusterPolicy) or namespaced resources (using the kind Policy.) As expected, namespaced policies will only apply to resources within the namespace in which they are defined while cluster-wide policies are applied to matching resources across all namespaces. Otherwise, there is no difference between the two types.

Additional policy types include PolicyException and (Cluster)CleanupPolicy which are separate resources and described further in the documentation.

### Architecture

![image](https://user-images.githubusercontent.com/43399466/219931795-dce93e3b-9f78-42ef-ba5e-9aa685252e2f.png)
