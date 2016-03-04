FROM oberthur/jenkins-slave-base:2.0

ENV MAVEN_VERSION 3.3.3
ENV MAVEN_HOME /usr/share/maven

RUN curl -fsSL http://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar xzf - -C /usr/share \
  && mv /usr/share/apache-maven-$MAVEN_VERSION /usr/share/maven \
  && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn
RUN wget -O /usr/bin/docker "https://get.docker.com/builds/Linux/x86_64/docker-1.7.1"
RUN chmod +x /usr/bin/docker


ENV JVM_OPTS="-server -Dcassandra.compaction.priority=1 -XX:+UseCompressedOops -XX:+AlwaysPreTouch" \
  CASSANDRA_VERSION=2.1.11 \
  DATASTAX_AGENT_VERSION=5.2.2

RUN echo "deb http://debian.datastax.com/community stable main" | tee -a /etc/apt/sources.list.d/cassandra.sources.list \
    && curl -L http://debian.datastax.com/debian/repo_key | apt-key add - \
    && apt-get update && apt-get install -y --force-yes sudo sysstat libjna-java dsc21=${CASSANDRA_VERSION}* cassandra=${CASSANDRA_VERSION}* cassandra-tools=${CASSANDRA_VERSION}* datastax-agent=${DATASTAX_AGENT_VERSION}* \
    && sed -i -e "s/^rpc_address: localhost/# rpc_address: localhost/" /etc/cassandra/cassandra.yaml \
    && sed -i -e "s/# rpc_interface: eth1/rpc_interface: eth0/" /etc/cassandra/cassandra.yaml \
    && sed -i -e 's/<appender-ref ref="FILE" \/>/\r/' /etc/cassandra/logback.xml \
    && mkdir /root/.cassandra \
    && echo "[connection]\nclient_timeout=120" >> /root/.cassandra/cqlshrc \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean 
