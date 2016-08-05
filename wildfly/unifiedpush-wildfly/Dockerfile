# Use latest rhmap/wildfly image as the base
FROM rhmap/wildfly

RUN mkdir -p /opt/jboss/wildfly/standalone/data && \
    mkdir -p /opt/jboss/wildfly/standalone/tmp/vfs/temp && \
    mkdir -p /opt/jboss/wildfly/standalone/log && \
    chmod -R 777 /opt/jboss/wildfly/standalone

USER root
ENV UPSDIST=/opt/ups

ADD artifacts/aerogear-unifiedpush-server-* $UPSDIST/

WORKDIR $UPSDIST

RUN mv aerogear-unifiedpush-server*/* .  && \
    rm -Rf aerogear-unifiedpush-server* && \
    unzip migrator/unifiedpush-migrator-*.zip -d migrator && \
    mv migrator/unifiedpush-migrator-*/* migrator/ && \
    touch $UPSDIST/migrator/liquibase.properties && \
    chmod -R 777 $UPSDIST/migrator/liquibase.properties

# Run everything below as aerogear user
USER jboss

# Switch to the working dir $JBOSS_HOME/standalone/deployments
WORKDIR /opt/jboss/wildfly/standalone/deployments

# copy war files
RUN cp $UPSDIST/servers/unifiedpush-auth-server.war $JBOSS_HOME/standalone/deployments \
    && cp $UPSDIST/servers/unifiedpush-server-wildfly.war $JBOSS_HOME/standalone/deployments


# copy and run startup script
# migration is done inside the startup script before launching the server
COPY entrypoint.sh /opt/
ENTRYPOINT ["/opt/entrypoint.sh"]
