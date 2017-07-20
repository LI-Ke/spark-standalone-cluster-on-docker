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

With this [docker-compose.yml](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/docker-compose.yml), we want to make a four-node spark standalone cluster of which one is for zookeeper, one is for kafka, one is for spark master and one is for spark worker. So we have created 4 servces in [docker-compose.yml](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/docker-compose.yml).

As you can see, we have defined an order for constructing this cluster step by step by using the key word <b>links</b>. Firstly, we need to build the image of the linked services. In [docker-compose.yml](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/docker-compose.yml), <b>spark master</b> linked by <b>spark worker</b> links <b>kafka</b>; <b>spark worker</b> links <b>spark master</b> and <b>kafka</b>; <b>kafka</b> links <b>zookeeper</b>. Therefore, the order to build the images of different components of this cluster is : <br/>1). zookeeper  <br/>2). kafka  <br/>3). spark master  <br/>4). spark worker

### zookeeper

To build an image for zookeeper, we need to specify the path of zookeeper's Dockerfile. Defining a hostname for a service will facilitate the access to it. Then we specify the default port <b>2181</b> for zookeeper and add a command :

```bash -c "kafka/bin/zookeeper-server-start.sh kafka/config/zookeeper.properties  && sleep 5s"```

which will start the zookeeper service when the zookeeper container is running.

### kafka

For kafka, we add <b>links</b> to indicate that to run a kafka, it's necessary to run a zookeeper firstly. The default port of kafka is <b>9092</b>. Since we are in a cluster mode and we only have one kafka broker in our cluster, we will define <b>KAFKA_BROKER_ID</b> as 0. the <b>KAFKA_HOST_NAME</b> is the ip of this container. Since the kafka container will take the second place and the first container will use 172.17.0.2, the ip of kafka is certainly <b>172.17.0.3</b>. It's not necessary to specify <b>KAFKA_ADVERTISED_PORT</b>. We connect kafka and zookeeper by defining <b>KAFKA_ZOOKEEPER_CONNECT</b> with <b>172.17.0.2:2181</b>

### spark master

The command:

```spark/bin/spark-class org.apache.spark.deploy.master.Master -h spark-master```

declares that this service will be used as spark master. And ```spark://spark-master:7077``` defines the host address of the master.

By default, port 7077 is for master and we need to submit our application to this port; the port for your application's dashboard is 4040; 8042 is the port for management web UI of Hadoop node; 8080 is the port for master web UI and 8088 is the port for Hadoop cluster web UI.

### spark worker

```spark/bin/spark-class org.apache.spark.deploy.worker.Worker spark://spark-master:7077```

declares this service as spark worker of the spark master spark://spark-master:7077. 8081 is the web UI port of this worker.

## Run cluster 

Go into this project where you can find [docker-compose.yml](https://github.com/LI-Ke/standalone-spark-cluster-on-docker/blob/master/docker-compose.yml). 

Run:

```docker-compose up -d```

