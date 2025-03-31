FROM registry.access.redhat.com/ubi8/openjdk-17:latest AS mvn-builder

COPY pom.xml ./
COPY src src

RUN mvn clean package

# https://docs.redhat.com/en/documentation/red_hat_jboss_enterprise_application_platform/8.0/html-single/using_jboss_eap_on_openshift_container_platform/index#assembly_building-and-running-jboss-eap-applicationson-openshift-container-platform_default

# Use EAP 8 Builder image to create a JBoss EAP 8 server
# with its default configuration

FROM registry.redhat.io/jboss-eap-8/eap8-openjdk17-builder-openshift-rhel8:latest AS builder

# Set up environment variables for provisioning. 
ENV GALLEON_PROVISION_FEATURE_PACKS org.jboss.eap:wildfly-ee-galleon-pack,org.jboss.eap.cloud:eap-cloud-galleon-pack
ENV GALLEON_PROVISION_LAYERS cloud-default-config
# Specify the JBoss EAP version  
ENV GALLEON_PROVISION_CHANNELS org.jboss.eap.channels:eap-8.0

# Run the assemble script to provision the server.
RUN /usr/local/s2i/assemble

# Copy the JBoss EAP 8 server from the builder image to the runtime image.
FROM registry.redhat.io/jboss-eap-8/eap8-openjdk17-runtime-openshift-rhel8:latest AS runtime

# Set appropriate ownership and permissions.
COPY --from=builder --chown=jboss:root $JBOSS_HOME $JBOSS_HOME

# COPY the helloworld WAR to $JBOSS_HOME/standalone/deployments
#COPY --chown=jboss:root target/helloworld.war $JBOSS_HOME/standalone/deployments/ROOT.war
COPY --from=mvn-builder --chown=jboss:root /home/jboss/target/helloworld.war $JBOSS_HOME/standalone/deployments/ROOT.war

# (optional) server modification. You can modify EAP server configuration:
# - First operation must always be embed-server.
RUN $JBOSS_HOME/bin/jboss-cli.sh --commands="embed-server,/system-property=Foo:add(value=Bar)"

# Copy a modified standalone.xml in $JBOSS_HOME/standalone/configuration/
#COPY --chown=jboss:root standalone.xml $JBOSS_HOME/standalone/configuration

# Ensure appropriate permissions for the copied files.
RUN chmod -R ug+rwX $JBOSS_HOME