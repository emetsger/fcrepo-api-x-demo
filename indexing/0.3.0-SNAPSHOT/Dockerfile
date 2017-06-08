FROM fcrepoapix/apix-karaf:4.0.7@sha256:b258ee07e6e578fc80b791aa0a02d54bcf18dc94acb7c3b340bea3ea8949f7c7

MAINTAINER Elliot Metsger <emetsger@jhu.edu>
LABEL description = "Provides a Karaf container configured with indexing features"

# The version of API-X features being included in the image
ENV APIX_VERSION=0.3.0-SNAPSHOT \
    FUSEKI_HOST=fuseki \
    FCREPO_HOST=fcrepo \
    FCREPO_PORT=80 \
    FCREPO_CONTEXT_PATH=/fcrepo \
    DEBUG_PORT=5010  \
    REINDEXING_PORT=9090

# Temporary for local development; copy any Maven artifacts into `maven/` that you want the image build process to see (e.g. locally build features that have yet to be published)
#ADD maven/ /build/repository/
RUN bin/start && \
    bin/client -r 10 -d 5  "feature:repo-add mvn:org.fcrepo.apix/fcrepo-api-x-karaf/${APIX_VERSION}/xml/features" && \
    bin/client -r 10 -d 5  "feature:install fcrepo-api-x-registry" && \
    bin/client -r 10 -d 5  "feature:install fcrepo-api-x-indexing" && \
    bin/client -r 10 -d 5  "feature:install fcrepo-indexing-triplestore" && \
    bin/stop


ENV JAVA_DEBUG_PORT=${DEBUG_PORT} \
    FCREPO_BASEURI=http://${FCREPO_HOST}:${FCREPO_PORT}${FCREPO_CONTEXT_PATH}/rest


# Copy Karaf feature configuration files into the image
COPY cfg/* etc/


# Update the Karaf logging configuration so that `bin/karaf` will echo logging
# to the console, viewable by `docker logs`

RUN sed -e "s/osgi:/stdout, osgi:/" -i etc/org.ops4j.pax.logging.cfg

# Temporary for local development; copy any Maven artifacts into `maven/` that you want the image build process to see (e.g. locally build features that have yet to be published)
#ADD maven/ /build/repository/

COPY entrypoint.sh /entrypoint.sh

RUN chmod 700 /entrypoint.sh

EXPOSE ${DEBUG_PORT} ${REINDEXING_PORT}
ENTRYPOINT [ "/entrypoint.sh" ]

# Launch Karaf with no console, and debugging enabled by default.
CMD [ "server", "debug" ]