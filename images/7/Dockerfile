FROM jboss/base-jdk:8

MAINTAINER rhuss@redhat.com

EXPOSE 8080

ENV TOMCAT_VERSION 7.0.70
ENV DEPLOY_DIR /maven

USER root

# Agent bond
# Agent bond including Jolokia and jmx_exporter
ADD agent-bond-opts /opt/run-java-options
RUN mkdir -p /opt/agent-bond \
 && curl http://central.maven.org/maven2/io/fabric8/agent-bond-agent/0.1.3/agent-bond-agent-0.1.3.jar \
          -o /opt/agent-bond/agent-bond.jar \
 && chmod 444 /opt/agent-bond/agent-bond.jar \
 && chmod 755 /opt/run-java-options
ADD jmx_exporter_config.yml /opt/agent-bond/
EXPOSE 8778 9779


# Get and Unpack Tomcat
RUN curl http://archive.apache.org/dist/tomcat/tomcat-7/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.tar.gz -o /tmp/catalina.tar.gz \
 && tar xzf /tmp/catalina.tar.gz -C /opt \
 && ln -s /opt/apache-tomcat-${TOMCAT_VERSION} /opt/tomcat \
 && chown -R jboss /opt/tomcat /opt/apache-tomcat-${TOMCAT_VERSION} \
 && rm /tmp/catalina.tar.gz

# Add roles
ADD tomcat-users.xml /opt/apache-tomcat-${TOMCAT_VERSION}/conf/

# Startup script
ADD deploy-and-run.sh /opt/apache-tomcat-${TOMCAT_VERSION}/bin/

RUN chmod 755 /opt/apache-tomcat-${TOMCAT_VERSION}/bin/deploy-and-run.sh \
 && rm -rf /opt/tomcat/webapps/examples /opt/tomcat/webapps/docs 

VOLUME [ "/opt/tomcat/logs", "/opt/tomcat/work", "/opt/tomcat/temp", "/tmp/hsperfdata_root" ]

# java-container-options for container specific java startup options
# Add java-container-options to /opt and make it executable
COPY java-container-options /opt/java-container-options
RUN chmod 755 /opt/java-container-options


ENV CATALINA_HOME /opt/tomcat
ENV PATH $PATH:$CATALINA_HOME/bin

CMD /opt/tomcat/bin/deploy-and-run.sh

USER jboss
