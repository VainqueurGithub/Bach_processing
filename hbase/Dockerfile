# HBase in Docker
#
# Version 0.5

# http://docs.docker.io/en/latest/use/builder/

FROM ubuntu:bionic

COPY *.sh /build/

ENV HBASE_VERSION 2.2.4

RUN /build/prepare-hbase.sh && \
    cd /opt/hbase && /build/build-hbase.sh \
    cd / && /build/cleanup-hbase.sh && rm -rf /build

# Install Oracle Java from PPA
RUN echo oracle-java7-installer shared/accepted-oracle-license-v1-1 select true | /usr/bin/debconf-set-selections && \
  apt-get install -y oracle-java7-installer oracle-java7-set-default

# Install dependencies
RUN apt-get install -y ant openssh-server zookeeperd vim telnet subversion rsync curl build-essential maven

# Download Hadoop
RUN wget -q 'https://archive.apache.org/dist/hadoop/core/hadoop-2.5.1/hadoop-2.5.1.tar.gz'
RUN wget -q 'http://archive.apache.org/dist/hbase/hbase-0.98.8/hbase-0.98.8-hadoop2-bin.tar.gz'
RUN wget -q 'https://protobuf.googlecode.com/files/protobuf-2.5.0.tar.gz'
RUN svn checkout http://svn.apache.org/repos/asf/nutch/branches/2.x/ nutch-sources

# Setup system user and group to own and run Hadoop
RUN addgroup hadoop && adduser --ingroup hadoop hduser
RUN usermod -a -G hadoop zookeeper

VOLUME /data

# Add Hadoop and HBase configs
ADD ./hbase-site.xml /opt/hbase/conf/hbase-site.xml

ADD ./zoo.cfg /opt/hbase/conf/zoo.cfg

ADD ./replace-hostname /opt/replace-hostname

ADD ./hbase-server /opt/hbase-server

ADD ./core-site.xml /opt/hbase/conf/core-site.xml
ADD ./mapred-site.xml /opt/hbase/conf/mapred-site.xml
ADD ./hdfs-site.xml /opt/hbase/conf/hdfs-site.xml


# REST API
EXPOSE 8080
# REST Web UI at :8085/rest.jsp
EXPOSE 8085
# Thrift API
EXPOSE 9090
# Thrift Web UI at :9095/thrift.jsp
EXPOSE 9095
# HBase's Embedded zookeeper cluster
EXPOSE 2181
# HBase Master web UI at :16010/master-status;  ZK at :16010/zk.jsp
EXPOSE 16010

CMD ["/opt/hbase-server"]
