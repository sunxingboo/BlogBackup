---
title: etcd简单服务搭建方式记录
permalink: etcd-build
date: 2020-07-08
tags: [etcd]
---

记录一下 etcd 搭建命令

<!--more-->

启动三个节点

```
sudo docker run -d -p 2379:2379 -p 2380:2380 \
--restart=always \
--net etcdnet \
--ip 192.167.0.168 \
--name etcd0 quay.io/coreos/etcd /usr/local/bin/etcd \
--name autumn-client0 \
-advertise-client-urls http://192.167.0.168:2379 \
-listen-client-urls http://0.0.0.0:2379 \
-initial-advertise-peer-urls http://192.167.0.168:2380 \
-listen-peer-urls http://0.0.0.0:2380 \
-initial-cluster-token etcd-cluster \
-initial-cluster autumn-client0=http://192.167.0.168:2380,autumn-client1=http://192.167.0.170:2480,autumn-client2=http://192.167.0.172:2580 \
-initial-cluster-state new

sudo docker run -d -p 2479:2479 -p 2480:2480 \
--restart=always \
--net etcdnet \
--ip 192.167.0.170 \
--name etcd1 quay.io/coreos/etcd /usr/local/bin/etcd \
--name autumn-client1 \
-advertise-client-urls http://192.167.0.170:2479 \
-listen-client-urls http://0.0.0.0:2479 \
-initial-advertise-peer-urls http://192.167.0.170:2480 \
-listen-peer-urls http://0.0.0.0:2480 \
-initial-cluster-token etcd-cluster \
-initial-cluster autumn-client0=http://192.167.0.168:2380,autumn-client1=http://192.167.0.170:2480,autumn-client2=http://192.167.0.172:2580 \
-initial-cluster-state new

sudo docker run -d -p 2579:2579 -p 2580:2580 \
--restart=always \
--net etcdnet \
--ip 192.167.0.172 \
--name etcd2 quay.io/coreos/etcd /usr/local/bin/etcd \
--name autumn-client2 \
-advertise-client-urls http://192.167.0.172:2579 \
-listen-client-urls http://0.0.0.0:2579 \
-initial-advertise-peer-urls http://192.167.0.172:2580 \
-listen-peer-urls http://0.0.0.0:2580 \
-initial-cluster-token etcd-cluster \
-initial-cluster autumn-client0=http://192.167.0.168:2380,autumn-client1=http://192.167.0.170:2480,autumn-client2=http://192.167.0.172:2580 \
-initial-cluster-state new
```

两种管理后台

```
docker run -d --name etcd-browser \
-p 8000:8000 \
--net etcdnet \
--env ETCD_HOST=192.167.0.168 \
--env ETCD_PORT=2379 \
buddho/etcd-browser

docker run -it -d --name etcdkeeper \
-p 8001:8080 \
--net etcdnet \
--env ETCDCTL_API=3 \
--env ETCD_ENDPOINTS="http://192.167.0.168:2379,http://192.167.0.170:2479,http://192.167.0.172:2579" \
deltaprojects/etcdkeeper
```

