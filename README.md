This repository contains example manifests for deploying Honeycomb's Kubernetes
integrations.

To start collecting cluster metrics data, grab your Honeycomb write key from
your [account page](https://ui.honeycomb.io/account). Then run
```
kubectl create secret generic honeycomb-writekey --from-literal=key=$YOUR_WRITEKEY -n kube-system

git clone https://github.com/honeycombio/kubernetes-manifests
kubectl apply -f kubernetes-manifests/metrics
```

After a few seconds, you should see the following datasets in the Honeycomb UI:

* `kubernetes-state-metrics`: This contains metrics describing the state of
  Kubernetes objects. For example, to see the number of running replicas per
  deployment, run the query
  - breakdown: deployment
  - calculate per group: MIN(kube_deployment_status_replicas)

* `kubernetes-resource-metrics`: This dataset contains metrics describing
  system resource use by pods and containers (CPU/memory/IO/etc). For example,
  to see the containers using the most CPU, run the query
  - break down: container_name
  - calculate per group: MAX(cpu/usage_rate)
  - order: MA(cpu/usage_rate) desc.

* `kubernetes-cluster-events`: This dataset contains events describing cluster
  state changes. It's similar to the output you'd see by running `kubectl get
  events --watch`. For example, you might use this dataset to set a
  [trigger](https://honeycomb.io/docs/guides/triggers/) on the query
  - calculate: COUNT
  - filter: kind = Pod, reason = Killing

  to alert you when too many pods are being killed.

_tip_: Under "graph settings", toggle "omit missing values" to get smoother
graphs.
