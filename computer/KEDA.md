---
type: architecuture
---
# Kubernetes Event-driven Autoscaling (KEDA)

[[Kubernetes]] Event-driven Autoscaling (KEDA) is a complementary autoscaling technology to the built-in Kubernetes Horizontal Pod Autoscaler (HPA). KEDA does not replace the HPA, it still uses it to do its magic.
The official KEDA website can be found here. KEDA has very good documentation so it is easy to set up. KEDA is an open-source Cloud Native project backed by [[Microsoft]] and it is fully supported in [[Azure]] [[AKS]] (Azure’s Kubernetes offering). It is used by big corporations such as Microsoft, Zapier and Alibaba cloud, and many others and is thus running in production at a massive scale.

## Why KEDA is a game-changer in the Kubernetes world

KEDA enables Kubernetes to scale pod replicas to zero and up, based on metrics such as the standard built-in Kubernetes metrics like CPU and memory but also based on advanced metrics such as queue depth of a message queue, requests/second, scheduled cron jobs, custom metrics from your own application logging and basically any other metric you can imagine. This is something the built-in HPA in Kubernetes can’t easily do. Here is a list of the scaling providers KEDA supports.

## How KEDA works

KEDA monitors metrics from an external metric provider system for example Azure Monitor and then scales based on scaling rules based on the value of the metric. It communicates directly with the metric provider system. It runs as a Kubernetes operator which is just a pod and continuously monitors.

![](https://miro.medium.com/max/1400/1*7t48ne8KZabS6BWMTgSatQ.png)

## How to setup KEDA

The easiest method of installing KEDA is to use a helm chart.

This can be done like so based on the [documentation](https://keda.sh/docs/2.6/deploy/):

```shell
helm repo add kedacore https://kedacore.github.io/charts
helm repo update
kubectl create namespace keda
helm install keda kedacore/keda --namespace keda
```

## Configuring scaling

Now that KEDA is installed and running in the cluster, you will need to write a manifest file to declare what to scale based on, when to scale and how to scale. I will provide guidance on how to set up scaling based on common metrics such as CPU and memory.

All supported scaling types and sources can be found here in the [documentation](https://keda.sh/docs/2.6/scalers/)

Scaling is defined as a manifest YAML file just like deployments and the kind is a ScaledObject.

#### Existing scaling objects can be queried with this command:

```shell
kubectl get scaledobject
```

#### And a scaled object can be deleted with this command:

```shell
kubectl delete scaledobject <name of scaled object>
```

### Memory scaling

A rule can be set up to scale based on memory usage inside of the container inside the pod.
The memory scaling documentation can be found [here](https://keda.sh/docs/2.6/scalers/memory/)

#### Scaling can be based on these types of values:

`Utilization`, the target value is the average of the resource metric across all relevant pods, represented as a percentage of the requested value of the resource for the pods.
`AverageValue`, the target value is the target value of the average of the metric across all relevant pods (quantity).

#### Utilization has been chosen for the particular manifest example below:

```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: memory-scaledobject
  namespace: default
spec:
  scaleTargetRef:
    name: sandboxapi
  triggers:
  - type: memory
    metadata:
      type: Utilization
      value: "50"
```

This example creates a rule that scales pods based on memory utilization.
The scaleTargetRef refers to the deployment that should be scaled.

### CPU scaling

Scaling can also be based on the CPU usage inside the container in the pod.

#### Scaling can be based on these types of values:

`Utilization`, the target value is the average of the resource metric across all relevant pods, represented as a percentage of the requested value of the resource for the pods.

`AverageValue`, the target value is the target value of the average of the metric across all relevant pods (quantity).

#### Utilization has been chosen for the particular manifest example below:

```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: memory-scaledobject
  namespace: default
spec:
  scaleTargetRef:
    name: sandboxapi
  triggers:
  - type: cpu
    metadata:
      type: Utilization
      value: "50"
```

This example creates a rule that scales pods based on current CPU utilization.
The scaleTargetRef refers to the deployment that should be scaled.

## Read more

1. https://medium.com/@casperrubaek/why-keda-is-a-game-changer-for-scaling-in-kubernetes-4ebf34cb4b61
2. KEAD Scalers: https://keda.sh/docs/2.6/scalers/