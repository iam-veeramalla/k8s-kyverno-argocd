# k8s-kyverno-argocd

Demo repository to enforce automated k8s cluster security using kyverno policy generator and argocd


![Screenshot 2023-02-19 at 12 40 48 PM](https://user-images.githubusercontent.com/43399466/219934201-b542599a-7f8a-4b72-a1bf-5db6ba1bfade.png)


## Installation

There are two easy ways to install kyverno:

![image](https://user-images.githubusercontent.com/43399466/219931795-dce93e3b-9f78-42ef-ba5e-9aa685252e2f.png)

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

## Demystifying Kyverno Policies 

A Kyverno policy is a collection of rules. Each rule consists of a match declaration, an optional exclude declaration, and one of a validate, mutate, generate, or verifyImages declaration. Each rule can contain only a single validate, mutate, generate, or verifyImages child declaration.

![image](https://user-images.githubusercontent.com/43399466/219931973-14c0f501-ae49-4cab-9da5-b01950cc308f.png)

Policies can be defined as cluster-wide resources (using the kind ClusterPolicy) or namespaced resources (using the kind Policy.) As expected, namespaced policies will only apply to resources within the namespace in which they are defined while cluster-wide policies are applied to matching resources across all namespaces. Otherwise, there is no difference between the two types.

Additional policy types include PolicyException and (Cluster)CleanupPolicy which are separate resources and described further in the documentation.
