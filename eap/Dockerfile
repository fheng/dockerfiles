# Use latest jboss/eap image as the base
FROM jboss-eap-6/eap64-openshift:1.3

# Run everything below as root user
USER root


# Install required packages. We need to enable only required repo
RUN yum install --disablerepo * --enablerepo rhel-7-server-rpms  -y unzip wget  && \
	yum -q clean all

## MYSQL
ENV mysql_module_dir=$JBOSS_HOME/modules/com/mysql/jdbc/main/ \
    NON_PROXY_HOSTS="" \
    HTTP_PROXY_HOST="" \
    HTTP_PROXY_PORT="" \
    HTTPS_PROXY_HOST="" \
    HTTPS_PROXY_PORT=""

RUN mkdir -p ${mysql_module_dir}
RUN wget -O mysql-connector-java-5.1.18.jar http://search.maven.org/remotecontent\?filepath\=mysql/mysql-connector-java/5.1.18/mysql-connector-java-5.1.18.jar
RUN mv mysql-connector-java-5.1.18.jar ${mysql_module_dir}
COPY configuration/xml/mysql-module.xml ${mysql_module_dir}/module.xml

# Rename the original configuration file
RUN mv $JBOSS_HOME/standalone/configuration/standalone.xml $JBOSS_HOME/standalone/configuration/standalone.xml.orig

# EAP configuration file ready for HTTPS
ADD configuration/xml/standalone-full-sample.xml $JBOSS_HOME/standalone/configuration/standalone.xml

# Add the certificate.sh script into $JBOSS_HOME/standalone/configuration/certs
ADD configuration/certs/ $JBOSS_HOME/standalone/configuration/certs

# Switch to $JBOSS_HOME/configuration/certs
WORKDIR $JBOSS_HOME/standalone/configuration/certs

# Execute the script to generate self signed certificates
RUN ./certificate.sh

# Switch to the working dir
WORKDIR $JBOSS_HOME

# Expose SSL default port
EXPOSE 8443
