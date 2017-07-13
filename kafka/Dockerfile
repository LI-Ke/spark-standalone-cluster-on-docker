FROM phusion/baseimage

MAINTAINER li_ke@yahoo.com

# Environment
ENV SCALA_VERSION 2.11
ENV KAFKA_VERSION 0.8.2.2

ENV HOME /usr/local
WORKDIR $HOME


# Install Java
RUN add-apt-repository ppa:webupd8team/java -y  && \
    apt-get update && \
    echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | debconf-set-selections && \
    apt-get --allow-unauthenticated install -y  oracle-java8-installer && \
    apt-get clean


# Install Kafka
RUN wget http://mirrors.dotsrc.org/apache/kafka/${KAFKA_VERSION}/kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz && \
    tar -xvzf kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz && \
    mv kafka_${SCALA_VERSION}-${KAFKA_VERSION} kafka && \
    rm kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz

# Configure environment variables
ENV KAFKA_HOME $HOME/kafka
ENV PATH $HOME/kafka/bin:$PATH

ADD start-kafka.sh /usr/bin/start-kafka.sh 

# The script needs to have executable permission
RUN chmod a+x /usr/bin/start-kafka.sh

CMD ["start-kafka.sh"]
