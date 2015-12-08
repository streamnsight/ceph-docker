docker-ceph
===========

Ceph-related dockerfiles

## Core Components:

* [`ceph/base`](base/):  Ceph base container image.  This is nothing but a fresh install of the latest Ceph on Ubuntu LTS (14.04)
* [`ceph/daemon`](daemon/): All-in-one container for all core daemons.
* [`ceph/mds`](mds/): _DEPRECATED (use `daemon`)_ Ceph MDS (Metadata server)
* [`ceph/mon`](mon/): _DEPRECATED (use `daemon`)_ Ceph Mon(itor)
* [`ceph/osd`](osd/): _DEPRECATED (use `daemon`)_ Ceph OSD (object storage daemon)
* [`ceph/radosgw`](radosgw/): _DEPRECATED (use `daemon`)_ Ceph Rados gateway service; S3/swift API server

## Utilities and convenience wrappers

* [`ceph/config`](config/): _DEPRECATED (base config done in `daemon`)_ Initializes and distributes cluster configuration
* [`ceph/docker-registry`](docker-registry/): Rados backed docker-registry images repository
* [`ceph/rados`](rados/): Convenience wrapper to execute the `rados` CLI tool
* [`ceph/rbd`](rbd/): Convenience wrapper to execute the `rbd` CLI tool
* [`ceph/rbd-lock`](rbd-lock/): Convenience wrapper to block waiting for an rbd lock
* [`ceph/rbd-unlock`](rbd-unlock/): Convenience wrapper to release an rbd lock
* [`ceph/rbd-volume`](rbd-volume/): Convenience wrapper to mount an rbd volume

## Demo

* [`ceph/demo`](demo/): Demonstration cluster for testing and learning.  This container runs all the major ceph components installed, bootstrapped, and executed for you to play with.  (not intended for use in building a production cluster)

## Video demonstration

### Manually

A recorded video on how to deploy your Ceph cluster entirely in Docker containers is available here:

[![Demo Running Ceph in Docker containers](http://img.youtube.com/vi/FUSTjTBA8f8/0.jpg)](http://youtu.be/FUSTjTBA8f8 "Demo Running Ceph in Docker containers")

### With Ansible

[![Demo Running Ceph in Docker containers with Ansible](http://img.youtube.com/vi/DQYZU1VsqXc/0.jpg)](http://youtu.be/DQYZU1VsqXc "Demo Running Ceph in Docker containers with Ansible")

### Using the daemon

IP=$(hostname -i) #note: this doesn't work on CoreOS, set the IP in your Unit file
```
# start a monitor daemon with etcd backend on network range 198.100.128.0/19
docker run -d --net=host --pid=host \
-v /etc/ceph:/etc/ceph \
-v /var/lib/ceph:/var/lib/ceph \
-e MON_IP=${IP} \
-e MON_NAME=$(hostname) \
-e CEPH_PUBLIC_NETWORK=198.100.128.0/19 \
-e HOSTNAME=$(hostname) \
-e KV_TYPE=etcd \
-e KV_IP=127.0.0.1 \
-e KV_PORT=4001 \
ceph/daemon mon > ceph_mon

# check the status
docker exec -it $(cat ceph_mon) ceph -s
# or 
docker exec -it $(cat ceph_mon) ceph -w
# or 
docker exec -it $(cat ceph_mon) ceph mon stat



# start an OSD with osd_directory
docker run -d --net=host --pid=host \
-v /var/run/ceph:/var/run/ceph \
-v /etc/ceph:/etc/ceph \
-v /var/lib/ceph:/var/lib/ceph \
-e MON_IP=${IP} \
-e MON_NAME=$(hostname) \
-e CEPH_PUBLIC_NETWORK=198.100.128.0/19 \
-e HOSTNAME=$(hostname) \
-e KV_TYPE=etcd \
-e KV_IP=127.0.0.1 \
-e KV_PORT=4001 \
-v /var/lib/ceph/osd:/var/lib/ceph/osd \
ceph/daemon osd_directory > ceph_osd



