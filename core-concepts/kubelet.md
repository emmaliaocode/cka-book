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

# Kubelet

## Responsibilities of Kubelet

1. Register the Node
2. Receive instructions to load a Pod on the Node
3. Request the container runtime engine to pull the required image
4. Monitor the status of the Pod and the Node

## Install Kubelet

Unlike other components, Kubeadm doesn't deploy Kubelet. Kubelet can only be deployed manually.

```sh
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubelet
```

View the options.

```sh
cat /etc/systemd/system/kubelet.service
```

```sh
# kubelet.service

ExecStart=/us/local/bin/kubelet \\
  --config=/var/lib/kubelet/kubelet-config.yaml \\
  --container-runtime=remote \\
  --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock \\
  --image-pull-progress-deadline=2m \\
  --kubeconfig=/var/lib/kubelet/kubeconfig \\
  --network-plugin=cni \\
  --register-node=true \\
  --v=2
```

View the running process.

```sh
ps -aux | grep kubelet
```

{% code overflow="wrap" %}
```sh
root    2462  1.2   2.1   837484   833872?     Ssl 17:31 0:08 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --cgroup-driver=cgroupfs --cni-bin-dir=/opt/cni/bin --cni-conf-dir=/etc/cni/net.d --network-plugin=cni
```
{% endcode %}
