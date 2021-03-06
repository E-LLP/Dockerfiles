#
#  Author: Hari Sekhon
#  Date: 2016-01-16 09:58:07 +0000 (Sat, 16 Jan 2016)
#
#  vim:ts=4:sts=4:sw=4:et
#
#  https://github.com/harisekhon/Dockerfiles
#
#  If you're using my code you're welcome to connect with me on LinkedIn and optionally send me feedback to help improve or steer this or other code I publish
#
#  https://www.linkedin.com/in/harisekhon
#

#FROM harisekhon/centos-java:jre7
# > 100MB smaller than centos
FROM harisekhon/debian-java:jre7
MAINTAINER Hari Sekhon (https://www.linkedin.com/in/harisekhon)

#ARG SCALA_VERSION=2.10
ARG SCALA_VERSION=2.11
#ARG KAFKA_VERSION=0.8.2.2
ARG KAFKA_VERSION=0.9.0.1
# 0.10 fails to come up
#ARG KAFKA_VERSION=0.10.0.0
ARG ADVERTISED_HOSTNAME=192.168.99.100

ENV PATH $PATH:/kafka/bin

LABEL Description="Apache Kafka by LinkedIn" \
      "Scala Version"="$SCALA_VERSION" \
      "Kafka Version"="$KAFKA_VERSION"

# quicker cached
#COPY kafka_$SCALA_VERSION-$KAFKA_VERSION /kafka

RUN apt-get update && \
    apt-get install -y --no-install-recommends wget tar && \
    wget -t 100 --retry-connrefused -O "kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz" "https://www.apache.org/dyn/closer.cgi?filename=/kafka/${KAFKA_VERSION}/kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz&action=download" && \
    tar zxvf "kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz" && \
    rm -fv "kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz" && \
    ln -sv "kafka_${SCALA_VERSION}-${KAFKA_VERSION}" kafka && \
    perl -pi.orig -e "s/\s*#?\s*advertised.host.name.*/advertised.host.name=${ADVERTISED_HOSTNAME}/" "kafka_${SCALA_VERSION}-${KAFKA_VERSION}/config/server.properties" && \
    # attempting to remove tar causes unmet dependency breakage
    apt-get purge -y wget && \
    apt-get autoremove -y && \
    apt-get clean

EXPOSE 2181 9092

CMD zookeeper-server-start.sh /kafka/config/zookeeper.properties & \
    echo "*** waiting for 10 secs to give ZooKeeper time to start up"; \
    sleep 10; \
    echo "# ============================================================================ #"; \
    echo "                         S t a r t i n g   K a f k a"; \
    echo "# ============================================================================ #"; \
    kafka-server-start.sh /kafka/config/server.properties
