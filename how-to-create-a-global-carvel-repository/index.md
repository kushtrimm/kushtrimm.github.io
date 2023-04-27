# How to create a global carvel repository



After attending KubeConEU in Amsterdam and meeting with the great Carvel project team, I have decided to explore more of the Carvel project tools. I have managed to create packages and package repositories and deploy them to a Kubernetes Cluster. One of the issues I faced was to see the package that I created in all namespaces within the cluster. This is done by using Carvel global repository. In this blog post, I will describe how to create a Carvel global repository.

## Prerequisites:

- Kubernetes Cluster (kind, TANZU, AKS, EKS, GKE)
- Kapp Controller installed (https://carvel.dev/kapp-controller/docs/v0.43.2/install/)


## Installation

If we want to install Carvel Packages in our Kubernetes Cluster, first we need to install a package repository, which is a collection of packages and their metadata (one of which we want to install, e.g. Grafana).

The YAML below is an example of a package repository:

``` yaml
apiVersion: packaging.carvel.dev/v1alpha1
kind: PackageRepository
metadata:
  annotations:
    kctrl.carvel.dev/repository-version: 0.2.0
  name: carvel-pkg-repo.DOMAIN.NAME
spec:
  fetch:
    imgpkgBundle:
      image: index.docker.io/REGISTRYNAME/carvel-pkg-repo@sha256:c1ee55cd199f63e823785d4457c228e7b432a0bfe4a646394438f51cae732e0d5d4
```

To apply this, we run the following command:

``` sh
kubectl apply -f package_repository.yml -n development 
```

Package repository CR is a namespaced resource, and in this case, it will create a package repository resource only in the development namespace. This means that Carvel packages will only be available in the development namespace. As a result, we can install the Grafana package only in the development namespace.

Let's say we have another namespace, e.g., staging, in which we need to install the same package (Grafana) as in the development namespace. Because PackageRepository is a namespaced CR, we would normally create the same resource in the staging namespace, e.g.

``` sh
kubectl apply -f package_repository.yml -n staging 
```

When the package repository is reconciled in the staging environment, the packages are available to be installed.

So far so good. But wait, not so good!!!

Let's say we have a new version of Grafana which is pushed to a newer version of Package Repository, e.g., 0.2.1. This means that we need to update the package repository in all namespaces. This will lead to a lot of misconfiguration and duplications between namespaces in the same Kubernetes Cluster.

To cover this, kapp-controller accepts a flag when it's installed, where you can define the global namespace: -packaging-global-namespace. By default, it comes with the following flag: kapp-controller-packaging-globa

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kapp-controller
  namespace: kapp-controller
spec:
    spec:
      containers:
      - args:
        - -packaging-global-namespace=kapp-controller-packaging-global
```

So in this case, you can create the package repository in the defined namespace e.g. kapp-controller-packaging-global

``` sh
kubectl apply -f package_repository.yml -n kapp-controller-packaging-global 
```

Now, the packages will be visible in all namespaces in the cluster.  So you can install Grafana package in development, staging and other namespaces withour adding the package repository in each namespace.

In case we want to exclude global package in specific namespaec we can add the following annotation to that namespace:

`packaging.carvel.dev/exclude-global-packages` 


## Conclusion

In case we have a central package repository where we keep all the packages, we should use the global repository feature in order to remove de-duplication and ease the management of version between namespace
