# Tripal Elasticsearch cross-site query

This repository is for demonstrating how to build elastic cluster which consists of nodes from multiple hosts.

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

# Launch tripal site container

```
sudo docker run -it -p 80:80 \
  -p 9201:9201 -p 9202:9202 \
  -p 9301:9301 -p 9302:9302 
  mingchen0919/docker-tripal-centos /bin/bash
```
