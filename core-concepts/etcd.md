# ETCD

## ETCD Basics

ETCD is a distributed reliable _key-value store_ that is simple, secure & fast.&#x20;

The Key-value stores information in the form of documents or pages. Each subject gets a documents and all information about that subject is stored within that file so that changes to one subject's file does not affect the others.

### Install ETCD

Follow the instructions from [GitHub](https://github.com/etcd-io/etcd/releases) to install ETCD or run ETCD with Docker.

Run ETCD with Docker.

```sh
rm -rf /tmp/etcd-data.tmp && mkdir -p /tmp/etcd-data.tmp && \
  docker rmi gcr.io/etcd-development/etcd:v3.4.33 || true && \
  docker run \
  -p 2379:2379 \
  -p 2380:2380 \
  --mount type=bind,source=/tmp/etcd-data.tmp,destination=/etcd-data \
  --name etcd-gcr-v3.4.33 \
  gcr.io/etcd-development/etcd:v3.4.33 \
  /usr/local/bin/etcd \
  --name s1 \
  --data-dir /etcd-data \
  --listen-client-urls http://0.0.0.0:2379 \
  --advertise-client-urls http://0.0.0.0:2379 \
  --listen-peer-urls http://0.0.0.0:2380 \
  --initial-advertise-peer-urls http://0.0.0.0:2380 \
  --initial-cluster s1=http://0.0.0.0:2380 \
  --initial-cluster-token tkn \
  --initial-cluster-state new \
  --log-level info \
  --logger zap \
  --log-outputs stderr

docker exec etcd-gcr-v3.4.33  /usr/local/bin/etcd --version
docker exec etcd-gcr-v3.4.33  /usr/local/bin/etcdctl --version
docker exec etcd-gcr-v3.4.33  /usr/local/bin/etcdctl endpoint health
docker exec etcd-gcr-v3.4.33  /usr/local/bin/etcdctl put foo bar
docker exec etcd-gcr-v3.4.33  /usr/local/bin/etcdctl get foo
```

### Operate ETCD

Run ETCD service.

```sh
./etcd
```

It starts a service that listens on port `2379` by default. Then attach any clients to the ETCD service to store and retrieve information.

A default client that come with ETCD is the `etcdctl` client that can be used for storing and retrieving key-value pairs.

```sh
./etcdctl set key1 value1
```

```sh
./etcdctl get key1
```

### Versions History

Knowing how ETCD versions evolved can be helpful when exploring ETCD and related articles online.&#x20;

* `v0.1` released in Aug 2013: The first version.
* `v0.5` released in Dec 2014.
* `v2.0` released in Feb 2015: Supported more than 10,000 writes per second with Raft Consensus Algorithm.
* `v3.1` released in Jan 2017: More optimizations and performance improvements.
* In Nov 2018, the ETCD project was incubated in Cloud Native Computing Foundation (CNCF).

A lots of changes were made in `v3.0`, so the API versions changed between `v2.0` and `v3.0`, meaning the `etcdctl` commands changed as well.

Check ETCD API version.

```sh
./etcdctl --version
```

```sh
etcdctl version: 3.3.11
API version: 2
```

Change `etcdctl` API version by setting or exporting the  environment variable `ETCDCTL_API` .

```sh
ETCDCTL_API=3 ./etcdctl --version
```

```sh
export ETCDCTL_API=3
./etcdctl --version
```

```sh
etcdctl version: 3.3.11
API version: 3.3
```

In `etcdctl v3.0` the command to set value is `etcdctl put`.

```sh
export ETCDCTL_API=3
./etcdctl put key1 value1
```

## ETCD in Kubernetes

The ETCD stores information of the Kubernetes cluster such as Nodes, Pods, Secrets, Roles, RoleBindings, etc. Every information printed with the `kubectl` command is from the ETCD server;  every change made is updated in the ETCD server.

### Setup Cluster Manually

If the Kubernetes cluster is setup manually, install ETCD from GitHub.

```sh
wget -q --https-only "https://github.com/coreos/etcd/releases/download/v3.3.9/etcd-v3.3.9-linux-amd64.tar-gz"
```

```sh
# etcd.service 

ExecStart=/usr/local/bin/etcd \
--name ${ETCD_NAME} \
--cert-file=/etc/etcd/kubernetes.pem \
--key-file=/etc/etcd/kubernetes-key.pem \
--peer-cert-file=/etc/etcd/kubernetes.pem \
--peer-key-file=/etc/etcd/kubernetes-key.pem \
--trusted-ca-file=/etc/etcd/ca.pem \
--peer-trusted-ca-file=/etc/etcd/ca.pem \
--peer-client-cert-auth \
--client-cert-auth \
--initial-advertise-peer-urls https://${INTERNAL_IP}:2380 \
--listen-peer-urls https://${INTERNAL_IP}:2380 \
--listen-client-urls https://${INTERNAL_IP}:2379,https://127.0.0.1:2379 \
--advertise-client-urls https://$(INTERNAL_IP}:2379 \
--initial-cluster-token etcd-cluster-0 \
--initial-cluster controller-0=https://${CONTROLLER0_IP}:2380,controller-1=https://${CONTROLLER1_IP}:2380 \
--initial-cluster-state new \
--data-dir=/var/lib/etcd
```

* `--advertise-client-urls`: The URL that should be configured on the Kube-ApiServer when it tries to reach the ETCD server.
* `--initial-cluster`: The different instances of the ETCD service in high availability (HA) environment.

### Setup Cluster with Kubeadm

If the Kubernetes cluster is setup with Kubeadm, then the ETCD server was deploy as a pod in `kube-system` Namespace.

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

List all keys stored by  run the `etcdctl` command.

```sh
kubectl exec etcd-master -n kube-system etcdctl get --prefix -keys-only
```

The Kubernetes stores data in the specific directory structure. The root directory is a registry, and under that are various Kubernetes constructions.

```shell-session
/registry/apiregistration.k8s.io/apiservices/v1.apps
/registry/apiregistration.k8s.io/apiservices/v1.authentication.k8s.io
/registry/apiregistration.k8s.io/apiservices/v1.authorization.k8s.io
/registry/apiregistration.k8s.io/apiservices/v1.autoscaling
/registry/apiregistration.k8s.io/apiservices/v1.batch
/registry/apiregistration.k8s.io/apiservices/v1.networking.k8s.io
/registry/apiregistration.k8s.io/apiservices/v1.rbac.authorization.k8s.io
/registry/apiregistration.k8s.io/apiservices/v1.storage.k8s.io
/registrv/apiregistration.k8s.io/apiservices/vlbetal.admissionregistration.k8s.io
...
```
