# Prometheus Operator

**Project status: *alpha*** Not all planned features are completed. The API, spec, status 
and other user facing objects are subject to change. We do not support backward-compability 
for the alpha releases.

The Prometheus Operator for Kubernetes provides easy monitoring definitions for Kubernetes
services and deployment and management of Prometheus instances.

Once installed, the Prometheus Operator provides the following features:

* **Create/Destroy**: Easily launch a Prometheus instance for your Kubernetes namespace,
  a specific application or team easily using the Operator.

* **Simple Configuration**: Configure the fundamentals of Prometheus like versions, persistence, 
  retention policies, and replicas from a native Kubernetes resource.

* **Target Services via Labels**: Automatically generate monitoring target configurations based
  on familiar Kubernetes label queries; no need to learn a Prometheus specific configuration language.

For an introduction to the Prometheus Operator, see the initial [blog
post](https://coreos.com/blog/the-prometheus-operator.html).

The current project roadmap [can be found here](./ROADMAP.md).

## Prometheus Operator vs. kube-prometheus

The Prometheus Operator makes the Prometheus configuration Kubernetes native
and manages and operates Prometheus and Alertmanager clusters. It is a piece of
the puzzle regarding full end-to-end monitoring.

[kube-prometheus](https://github.com/coreos/kube-prometheus) is a collection of
manifests to help getting started with monitoring Kubernetes itself and 
applications running on top of it.

## Prerequisites

Version `>=0.2.0` of the Prometheus Operator requires a Kubernetes
cluster of version `>=1.5.0`. If you are just starting out with the 
Prometheus Operator, it is highly recommended to use the latest version.

If you have previously used pre-1.5.0 releases of Kubernetes with the `0.1.0` 
version of the Prometheus Operator, see the [migration](#migration) section.

## Migration

The `PetSet` was deprecated in the `1.5.0` release of Kubernetes in favor of
the `StatefulSet`. As the Prometheus Operator used the `PetSet` in version
`0.1.0`, those need to be migrated as we upgrade our Kubernetes cluster as well
as the Prometheus Operator.

First the Prometheus Operator needs to be shut down. Once shut down, retrieve 
the `PetSet`s that were generated by it. You can do so simply by finding all
`Prometheus` and `Alertmanager` objects created:

```
kubectl get prometheuses --all-namespaces
kubectl get alertmanagers --all-namespaces
```

For each `Prometheus` and `Alertmanger` object, a respective `PetSet` with the
same name was created in the same namespace. Those `PetSet`s need to be
migrated according to the [official migration documentation](http://kubernetes.io/docs/tasks/manage-stateful-set/upgrade-pet-set-to-stateful-set/).

Once migrated and on Kubernetes version `>=1.5.0`, you can start the
Prometheus Operator of version `>=0.2.0`, and the `StatefulSet` created
in the migration will from now on be managed by the Prometheus Operator.

## Third party resources

The Operator acts on the following [third party resources (TPRs)](http://kubernetes.io/docs/user-guide/thirdpartyresources/):

* **[`Prometheus`](./Documentation/prometheus.md)**, which defines a desired Prometheus deployment.
  The Operator ensures at all times that a deployment matching the resource definition is running.

* **[`ServiceMonitor`](./Documentation/service-monitor.md)**, which declaratively specifies how groups
  of services should be monitored. The Operator automatically generates Prometheus scrape configuration
  based on the definition.

* **[`Alertmanager`](./Documentation/alertmanager.md)**, which defines a desired Alertmanager deployment.
  The Operator ensures at all times that a deployment matching the resource definition is running.


## Installation

Install the Operator inside a cluster by running the following command:

```
kubectl apply -f deployment.yaml
```

To run the Operator outside of a cluster:

```
make
hack/run-external.sh <kubectl cluster name>
```

**The Prometheus Operator collects anonymous usage statistics to help us learning how the software is being used and how we can improve it. To disable collection, run the Operator with the flag `-analytics=false`**
