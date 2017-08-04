# Tripal Elasticsearch cross-site query

This repository is for demonstrating how to build elastic cluster which consists of nodes from multiple hosts. These elastic clusters can be used for development of Tripal Elasticsearch cross-site querying.

In this example, two jetstream VMs are luanched. A tripal docker container (https://github.com/tripal/docker-tripal-centos) is then
launched within each VM. Each container deploys a Tripal site and two Elasticsearch instances. 

To make it easier to explain, I give different names to the the two docker containers and fours elasticsearch instances, respectively:
<code>
* host1: the docker container running on VM 1, which has IP address `149.165.157.49`
* host2: the docker container running on VM 2, which has IP address `149.165.157.84`

* host1-cluster1-node1: elastic instance running from container 1 (host1)
* host2-cluster1-node2: elastic instance running from container 2 (host2)
* host1-cluster2-node1: elastic instance running from container 1 (host1)
* host2-cluster2-node1: elastic instance running from container 2 (host2)
</code>
There are two elasticsearch clusters. Each cluster has two nodes from two different hosts.


# Launch two Jetstream VMs

Select a Jetstream VM which already has Docker installed. Here I used the image **Ubuntu 16_04 Devel and Docker**.

## Launch tripal site on host 1 (ip: 149.165.157.49)

First, ssh into VM 1.

```
ssh your-jetstream-id@149.165.157.49
```

Second, launch the docker container ([more details about the docker image used](https://github.com/tripal/docker-tripal-centos)).

There are two elasticsearch instances running from within container. These two instances user `http.port` 9201 and 9202, respectively. Therefore, we need to expose these two ports (9201 and 9202) to the host, which here is the **VM 1**. 

We also needs to expose two another ports for **transport tcp ports**. Here port 9301 and 9302 are used. And these two ports need to be specified in the `elasticsearch.yml` file.

```
sudo docker run -it -p 80:80 \
  -p 9201:9201 -p 9202:9202 \
  -p 9301:9301 -p 9302:9302 
  mingchen0919/docker-tripal-centos /bin/bash
```

## Launch tripal site on host 2 (ip: 149.165.157.49)

First, ssh into VM 2.

```
ssh your-jetstream-id@149.165.157.84
```

Second, launch the docker container.

```
sudo docker run -it -p 80:80 \
  -p 9201:9201 -p 9202:9202 \
  -p 9301:9301 -p 9302:9302 
  mingchen0919/docker-tripal-centos /bin/bash
```

# Config `elasticsearch.yml` file.

## Elastichsearch instance 1 on host 1: 

Name: **host1-cluster1-node1**
Location: **/etc/elasticsearch_cluster-01-node-01/elasticsearch.yml**.

Replace the content with content below:

```
#----cluster name---------------
cluster.name : my-cluster-01

#----node module settings-------
node.name : host1-cluster1-node1
node.master: true
node.data: true
path.data : /var/lib/elasticsearch_cluster-01-node-01
path.logs : /var/log/elasticsearch_cluster-01-node-01

#----network module settings--------
network.publish_host: 149.165.157.49
network.bind_host: 0.0.0.0

#----transport module settings------
http.port : 9201
transport.tcp.port: 9301

#----discovery module settings------
discovery.zen.minimum_master_nodes: 2
discovery.zen.ping.unicast.hosts: ["149.165.157.84"]

#----configure swapiness------------
bootstrap.memory_lock : true
```

## Elastichsearch instance 2 on host 2: 

Name: **host2-cluster1-node2**
Location: **/etc/elasticsearch_cluster-01-node-01/elasticsearch.yml**.

Replace the content with content below:

```
#----cluster name---------------
cluster.name : my-cluster-01

#----node module settings-------
node.name : host2-cluster1-node2
node.master: true
node.data: true
path.data : /var/lib/elasticsearch_cluster-01-node-01
path.logs : /var/log/elasticsearch_cluster-01-node-01

#----network module settings--------
network.publish_host: 149.165.157.84
network.bind_host: 0.0.0.0

#----transport module settings------
http.port : 9201
transport.tcp.port: 9301

#----discovery module settings------
discovery.zen.minimum_master_nodes: 2
discovery.zen.ping.unicast.hosts: ["149.165.157.49"]

#----configure swapiness------------
bootstrap.memory_lock : true
```

## Elastichsearch instance 3 on host 1: 

Name: **host1-cluster2-node1**
Location: **/etc/elasticsearch_cluster-01-node-02/elasticsearch.yml**.

```
#----cluster name---------------
cluster.name : my-cluster-02

#----node module settings-------
node.name : host1-cluster2-node1
node.master: true
node.data: true
path.data : /var/lib/elasticsearch_cluster-01-node-02
path.logs : /var/log/elasticsearch_cluster-01-node-02

#----network module settings--------
network.publish_host: 149.165.157.49
network.bind_host: 0.0.0.0

#----transport module settings------
http.port : 9202
transport.tcp.port: 9302

#----discovery module settings------
discovery.zen.minimum_master_nodes: 2
discovery.zen.ping.unicast.hosts: ["149.165.157.84"]

#----configure swapiness------------
bootstrap.memory_lock : true
```

## Elastichsearch instance 4 on host 2: 

Name: **host2-cluster2-node2**
Location: **/etc/elasticsearch_cluster-01-node-02/elasticsearch.yml**.

```
#----cluster name---------------
cluster.name : my-cluster-02

#----node module settings-------
node.name : host2-cluster2-node2
node.master: true
node.data: true
path.data : /var/lib/elasticsearch_cluster-01-node-02
path.logs : /var/log/elasticsearch_cluster-01-node-02

#----network module settings--------
network.publish_host: 149.165.157.84
network.bind_host: 0.0.0.0

#----transport module settings------
http.port : 9202
transport.tcp.port: 9302

#----discovery module settings------
discovery.zen.minimum_master_nodes: 2
discovery.zen.ping.unicast.hosts: ["149.165.157.49"]

#----configure swapiness------------
bootstrap.memory_lock : true
```
