# Running Apache Spark Standalone Cluster on Docker

Have to admit that it's not easy to deploy a spark standalone cluster totally successfully on docker, because of so many parametres to which we have to pay attention. Here, I will show you how to create a docker compose file for spark cluster manually and then how to run it and test it with a spark streaming application.

I have made a simple example of a spark cluster with 4 nodes. One is for zookeeper, one is for kafka, one is for spark master and the last one is for spark worker. The number of spark worker is scalable. We just need to add some spark workers as services in [docker-compose.yml](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/docker-compose.yml).

I will begin from the dockerfiles in different components, then [docker-compose.yml](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/docker-compose.yml) and then the test of the cluster by deploying an application.

## Dockerfile for zookeeper
