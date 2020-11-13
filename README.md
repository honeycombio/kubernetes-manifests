# **This project is no longer under development!**
This project is not actively maintained and you should not use it. New issues and pull requests will likely be ignored. 
Refer to [documentation](https://docs.honeycomb.io/getting-data-in/integrations/kubernetes/) for installation options of
the [Honeycomb Kubernetes Agent](https://github.com/honeycombio/honeycomb-kubernetes-agent).

---




This repository contains example manifests for deploying Honeycomb's Kubernetes
integrations.

The Honeycomb [Kubernetes agent](https://github.com/honeycombio/honeycomb-kubernetes-agent) provides a flexible way to ingest logs from applications running on Kubernetes.  Honeycomb can also leverage [Heapster](https://github.com/honeycombio/heapster) and [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics) to explore your cluster's state and resource use.

To start collecting data, grab your Honeycomb write key from
your [account page](https://ui.honeycomb.io/account). Then run
```
kubectl create secret generic honeycomb-writekey --from-literal=key=$YOUR_WRITEKEY -n default
```
to save your write key as a Kubernetes secret.

Next, clone this repository and apply the manifests:

```
git clone https://github.com/honeycombio/kubernetes-manifests
```

## Kubernetes Logs Agent

### For a generic K8S cluster:
```
kubectl apply -f kubernetes-manifests/logs/quickstart.yaml
```

### Google Kubernetes Engine

You might need to grant your the role `cluster-admin` before installing:

```
kubectl create clusterrolebinding your-user-admin-binding --clusterrole=cluster-admin --user=your.user.email@example.com
```

Then install the agent:

```
kubectl apply -f kubernetes-manifests/logs/quickstart-gke.yaml
```
### Microsot AKS
```
kubectl apply -f kubernetes-manifests/logs/quickstart-aks.yaml
```

### Amazon EKS
```
kubectl apply -f kubernetes-manifests/logs/quickstart-aws-eks.yaml
```

## Metrics Agent

```
kubectl apply -f kubernetes-manifests/metrics
```

After a few seconds, you should see the following datasets in the Honeycomb UI:

* `kubernetes-resource-metrics`: This dataset contains metrics describing
  system resource use by pods and containers (CPU/memory/IO/etc). For example,
  to see the containers using the most CPU, run the query
  - break down: container_name
  - calculate per group: MAX(cpu/usage_rate)
  - order: MAX(cpu/usage_rate) desc.

* `kubernetes-cluster-events`: This dataset contains events describing cluster
  state changes. It's similar to the output you'd see by running `kubectl get
  events --watch`. For example, you might use this dataset to set a
  [trigger](https://docs.honeycomb.io/api/triggers/) on the query
  - calculate: COUNT
  - filter: kind = Pod, reason = Killing

  to alert you when too many pods are being killed.

* `kubernetes-logs`: This contains logs from applications running on
  Kubernetes. Note that by default, the agent only parses logs from Kubernetes
  system components. You'll want to adjust its configuration to parse logs from
  _your_ applications! See the agent
  [docs](https://docs.honeycomb.io/getting-data-in/integrations/kubernetes/configuration/) for
  configuration details.
