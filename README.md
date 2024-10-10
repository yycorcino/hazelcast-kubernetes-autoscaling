<a name="readme-top"></a>

<!-- PROJECT LOGO -->
<div align="center">
  <h3 align="center">Hazelcast and Kubernetes</h3>

  <p align="center">
    An attempt to setup Hazelcast autoscaling depending on CPU usage on a single machine.
  </p>
</div>

<!-- ABOUT THE PROJECT -->

### About The Project

This project follows [Mesut Celik at Hazelcast](https://hazelcast.com/blog/autoscaling-with-horizontal-pod-autoscaler/). My version is a Hazelcast cluster on your local computer with Minikube and Kubernetes. The issue I ran into is after Step 4 with metrics unavailable. The HPA information; "targets" attribute is "unknown/50%" and doesn't allow for autoscaling. The cause is due to the cluster not having information of the node CPU usage and therefore cluster doesn't know when to scale.

### Prerequisites:

Required packages needed.

- Helm
- Kubernetes
- Minikube

### Steps

To get started, check out `diagrams/autoscaling_architecture.drawio`.

1. Start Minikube tool that allows Kubernetes cluster on local computer.

```
minikube start
```

2. Run to enable metrics-server and verify if available is True.

```
minikube addons enable metrics-server
```

```
kubectl get apiservices | grep metrics
```

3. Use Helm to install Hazelcast.

```
helm repo add hazelcast https://hazelcast-charts.s3.amazonaws.com/
```

```
helm repo update
```

4. Configure autoscaling Hazelcast with default 3 nodes and max of 10 nodes.

```
helm install hz-hazelcast hazelcast/hazelcast
```

```
kubectl autoscale statefulset hz-hazelcast --cpu-percent=50 --min=3 --max=10
```

5. Add usage to CPU at node 0.

```
kubectl exec hz-hazelcast-0 yes > /dev/null
```

### Utility Command to Navigate

- Display Horizontal Pod Autoscalers (HPA) Information.

```
kubectl get hpa
```

- Get all pods.

```
kubectl get pods
```

- Delete Kubernete cluster.

```
kubectl delete hpa hazelcast
```

- Delete all nodes at all namespaces.

```
kubectl delete pods --all --all-namespaces
```

- Get node ip address.

```
kubectl get pod hz-hazelcast-0 -o jsonpath='{.status.podIP}'
```
