FROM       centos:centos7
MAINTAINER Sonatype <cloud-ops@sonatype.com>

ENV SONATYPE_WORK /sonatype-work
ENV NEXUS_VERSION 3.0.0-b2015110601

RUN yum install -y \
  curl tar createrepo \
  && yum clean all

RUN cd /var/tmp \
  && curl --fail --silent --location --retry 3 -O \
  --header "Cookie: oraclelicense=accept-securebackup-cookie; " \
  http://download.oracle.com/otn-pub/java/jdk/7u76-b13/jdk-7u76-linux-x64.rpm \
  && rpm -Ui jdk-7u76-linux-x64.rpm \
  && rm -rf jdk-7u76-linux-x64.rpm

RUN cd /var/tmp \
  && curl --fail --silent --location --retry 3 -O \
    https://sonatype-download.global.ssl.fastly.net/nexus/oss/nexus-installer-${NEXUS_VERSION}-linux-rpm.rpm \
  && rpm -Ui nexus-installer-${NEXUS_VERSION}-linux-rpm.rpm \
  && rm -rf nexus-installer-${NEXUS_VERSION}-linux-rpm.rpm

RUN useradd -r -u 200 -m -c "nexus role account" -d ${SONATYPE_WORK} -s /bin/false nexus

VOLUME ${SONATYPE_WORK}

EXPOSE 8081
WORKDIR /opt/sonatype/nexus
USER nexus
ENV CONTEXT_PATH /
ENV MAX_HEAP 768m
ENV MIN_HEAP 256m
ENV JAVA_OPTS -server -XX:MaxPermSize=192m -Djava.net.preferIPv4Stack=true
ENV LAUNCHER_CONF ./conf/jetty.xml ./conf/jetty-requestlog.xml
CMD java \
  -Dnexus-work=${SONATYPE_WORK} -Dnexus-webapp-context-path=${CONTEXT_PATH} \
  -Xms${MIN_HEAP} -Xmx${MAX_HEAP} \
  -cp 'conf/:lib/*' \
  ${JAVA_OPTS} \
  org.sonatype.nexus.bootstrap.Launcher ${LAUNCHER_CONF}

