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

# Controller Manager

Controller Manager manages various controller in Kubernetes. A controller is a process that continuously monitors the state of various components within the system and brings the whole system to the desired functioning state.

## How Controller works?

### Example: Node Controller

The Node Controller gets the status of the Nodes every 5 seconds (`--node-monitor-period=5s`) through the API Server. If the API Server stop receiving status report from a Node for 40 seconds (`--node-monitor-grace-period=40s`), the Node is marked as `unreachable`.

### Example: Replication Controller

The Node Controller monitors the status of ReplicaSets and ensuring that the desired number of Pods are available.

## Install Controller Manager

### Setup Manually

```sh
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-controller-manager
```

View the options.

```sh
cat /etc/systemd/system/kube-controller-manager.service
```

```sh
# kube-controller-manager.service

[Service]
ExecStart=/usr/local/bin/kube-controller-manager \\
  --address=0.0.0.0 \\
  --cluster-cidr=10.200.0.0/16 \\
  --cluster-name=kubernetes \\
  --cluster-signing-cert-file=/var/lib/kubernetes/ca.pem \\
  --cluster-signing-key-file=/var/lib/kubernetes/ca-key.pem \\
  --kubeconfig=/var/lib/kubernetes/kube-controller-manager.kubeconfig \\
  --leader-elect=true \\
  --root-ca-file=/var/lib/kubernetes/ca.pem \\
  --service-account-private-key-file=/var/lib/kubernetes/service-account-key.pem \\
  --service-cluster-ip-range=10.32.0.0/24 \\
  --use-service-account-credentials=true \\
  --v=2 \\
  --node-monitor-period=5s \\
  --node-monitor-grace-period=40s \\
  --controllers=bootstrap-signer-controller,certificatesigningrequest-approving-controller,certificatesigningrequest-cleaner-controller,certificatesigningrequest-signing-controller,cloud-node-lifecycle-controller,clusterrole-aggregation-controller,cronjob-controller,daemonset-controller,deployment-controller,disruption-controller,endpoints-controller,endpointslice-controller,endpointslice-mirroring-controller,ephemeral-volume-controller,garbage-collector-controller,horizontal-pod-autoscaler-controller,job-controller,legacy-serviceaccount-token-cleaner-controller,namespace-controller,node-ipam-controller,node-lifecycle-controller,node-route-controller,persistentvolume-attach-detach-controller,persistentvolume-binder-controller,persistentvolume-expander-controller,persistentvolume-protection-controller,persistentvolumeclaim-protection-controller,pod-garbage-collector-controller,replicaset-controller,replicationcontroller-controller,resourceclaim-controller,resourcequota-controller,root-ca-certificate-publisher-controller,service-cidr-controller,service-lb-controller,serviceaccount-controller,serviceaccount-token-controller,statefulset-controller,storage-version-migrator-controller,storageversion-garbage-collector-controller,taint-eviction-controller,token-cleaner-controller,ttl-after-finished-controller,ttl-controller,validatingadmissionpolicy-status-controller
```

* `--node-monitor-period=5s`: The `node-controller` syncs Node status every 5s.
* `--node-monitor-grace-period=40s`: The Node status is allowed to be unresponsive for 40s before marking it `Unhealthy`.

### Setup with Kubeadm

If the Kubernetes cluster is set with Kubeadm, then the Controller Manager was deployed as a pod in `kube-system` Namespace.

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
cat /etc/kubernetes/manifests/kube-controller-manager.yaml
```

```sh
spec:
containers:
  - command:
  - kube-controller-manager
  - --address=127.0.0.1
  - --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt
  - --cluster-signing-key-file=/etc/kubernetes/pki/ca.key
  - --controllers=*,bootstrapsigner,tokencleaner
  - --kubeconfig=/etc/kubernetes/controller-manager.conf
  - --leader-elect=true
  - --root-ca-file=/etc/kubernetes/pki/ca.crt
  - service-account-private-key-file=/etc/kubernetes/pki/sa.key
  - --use-service-account-credentials=true
```

View the running process.

```sh
ps-aux | grep kube-controller-manager
```

{% code overflow="wrap" %}
```sh
root    1994  2.7   5.1  154360  105024 ?     Ssl    06:45   1:25 kube-controller-manager --address=127.0.0.1 --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt --cluster-signing-key-file=/etc/kubernetes/pki/ca.key --controllers=*,bootstrapsigner,tokencleaner --kubeconfig=/etc/kubernetes/controller-manager.conf --leader-elect=true --root-ca-file=/etc/kubernetes/pki/ca.crt --service-account-private-key-file=/etc/kubernetes/pki/sa.key --use-service-account-credentials=true
```
{% endcode %}
