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

### ETCD Versions History

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
