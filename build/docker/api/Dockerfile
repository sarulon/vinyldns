FROM hseeberger/scala-sbt:11.0.7_1.3.10_2.11.12

ARG BRANCH=master
ARG VINYLDNS_VERSION

RUN git clone -b ${BRANCH} --single-branch --depth 1 https://github.com/vinyldns/vinyldns.git /vinyldns

# The default jvmopts are huge, meant for running everything, use a paired down version
COPY .jvmopts /vinyldns

RUN cd /vinyldns ; sbt "set version in ThisBuild := \"${VINYLDNS_VERSION}\"" api/stage

FROM adoptopenjdk/openjdk11:jdk-11.0.7_10-alpine

RUN apk add --update --no-cache netcat-openbsd bash

COPY --from=builder /vinyldns/modules/api/target/universal/stage /opt/docker

# This will set the vinyldns version, make sure to have this in config... version = ${?VINYLDNS_VERSION}
ARG VINYLDNS_VERSION
ENV VINYLDNS_VERSION=$VINYLDNS_VERSION

RUN mkdir -p /opt/docker/lib_extra

# Mount the volume for config file and lib extras
# Note: These volume names are used in the build.sbt
VOLUME ["/opt/docker/lib_extra/", "/opt/docker/conf"]

EXPOSE 9000

ENTRYPOINT ["/opt/docker/bin/api"]
