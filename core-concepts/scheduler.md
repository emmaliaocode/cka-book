---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Scheduler

The Scheduler is responsible for scheduling Pods to Nodes. It only decides which Pod goes on which Node, and doesn't actually place Pod on the Node, which is the job of Kubelet.

## How Scheduler Works?

In Kubernetes, the Scheduler decides which Node to place a Pod in depending on certain criteria. The Scheduler looks for each Node and tries to find the best Node for the Pod.

For example, if the Scheduler needs to schedule a Pod that requires 10 CPUs, it will

1. Filter Nodes with insufficient CPUs (less than 10 CPUs).
2. Rank the Nodes to identify the best fit for the Pod (16 CPUs is greater than 12 CPUs).

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>How Scheduler works?</p></figcaption></figure>

## Strategies for Scheduling

Strategies for scheduling are covered in the future sections, including:

1. Resources Requirements and Limits
2. Taints and Tolerations
3. Node Selectors/Affinity

## Install Scheduler

### Setup Manually

```sh
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-scheduler
```

View the options.

```sh
cat /etc/systemd/system/kube-scheduler.service
```

```sh
# kube-scheduler.service

ExecStart=/us/local/bin/kube-scheduler \\
  --config=/etc/kubernetes/config/kube-scheduler.yaml \\
  --v=2
```

### Setup with Kubeadm

If the Kubernetes cluster is set with Kubeadm, then the Scheduler was deployed as a pod in `kube-system` Namespace.

```sh
kubectl get pods -n kube-system
```

```sh
NAMESPACE     NAME                            READY  STATUS    RESTARTS   AGE
kube-system   coredns-78fcdf6894-alezl        1/1    Running   0          1h
kube-system   coredns-78fcdf6894-ep7oq        1/1    Running   0          1h
kube-system   etcd-master                     1/1    Running   0          1h
kube-system   kube-apiserver-master           1/1    Running   0          1h
kube-system   kube-controller-manager-master  1/1    Running   0          1h
kube-system   kube-proxy-ke3r6                1/1    Running   0          1h
kube-system   kube-proxy-ejitw                1/1    Running   0          1h
kube-system   kube-scheduler-master           1/1    Running   0          1h
kube-system   weave-net-ifjkf                 2/2    Running   1          1h
kube-system   weave-net-cerze                 2/2    Running   1          1h
```

View the options.

```sh
cat /etc/kubernetes/manifests/kube-scheduler.yaml
```

```sh
spec:
  containers:
  - command:
    - kube-scheduler
    - --address=127.0.0.1
    - --kubeconfig=/etc/kubernetes/scheduler.conf
    - --leader-elect=true
```

View the running process.

```sh
ps -aux | grep kube-scheduler
```

{% code overflow="wrap" %}
```sh
root    2477  0.8   1.6   48524   34044 ?     Ssl 17:31 0:08 kube-scheduler --address=127.0.0.1 --kubeconfig=/etc/kubernetes/scheduler.conf --leader-elect=true
```
{% endcode %}
