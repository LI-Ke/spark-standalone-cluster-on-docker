# Running Apache Spark Standalone Cluster on Docker

Have to admit that it's not easy to deploy a spark standalone cluster totally successfully on docker, because of so many parametres to which we have to pay attention. Here, I will show you how to create a docker compose file for spark cluster manually and then how to run it and test it with a spark streaming application.

I have made a simple example of a spark cluster with 4 nodes. One is for zookeeper, one is for kafka, one is for spark master and the last one is for spark worker. The number of spark worker is scalable. We just need to add some spark workers as services in [docker-compose.yml](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/docker-compose.yml).

I will begin from the dockerfiles in different components, then [docker-compose.yml](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/docker-compose.yml) and then the test of the cluster by deploying an application.

## Dockerfile for zookeeper

Since there is zookeeper in kafka_2.11-0.8.2.2.tgz, we will use the inside zookeeper to ensure the compatibility with kafka. Here is the [Dockerfile](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/zookeeper/Dockerfile). We just need to implement a java environment to before 

## Dockerfile for kafka

In this [Dockerfile](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/kafka/Dockerfile), We will use the same kafka package kafka_2.11-0.8.2.2.tgz and we will write the same docker code as the docker file for zookeeper except that we will add several commands to start kafka (Actually we need to start zookeeper firstly, so we'll add the start command for zookeeper in [docker-compose.yml](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/docker-compose.yml)).

## Dockerfile for spark

Since our object is to run a spark streaming applicaion which is a sbt project on a spark cluster, we need to install the environments of java, scala, sbt, spark and spark streaming for a cluster node in this [Dockerfile](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/spark/Dockerfile).

## Docker Compose

This is a vital part, because the cluster might not work if we don't configure some very important parametres.

With this [docker-compose.yml](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/docker-compose.yml), we want to make a four-node spark standalone cluster.
