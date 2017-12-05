FROM docker:dind

#######OPENJDK##############

ENV LANG C.UTF-8

# add a simple script that can auto-detect the appropriate JAVA_HOME value
# based on whether the JDK or only the JRE is installed
RUN { \
		echo '#!/bin/sh'; \
		echo 'set -e'; \
		echo; \
		echo 'dirname "$(dirname "$(readlink -f "$(which javac || which java)")")"'; \
	} > /usr/local/bin/docker-java-home \
	&& chmod +x /usr/local/bin/docker-java-home
ENV JAVA_HOME /usr/lib/jvm/java-1.8-openjdk
ENV PATH $PATH:/usr/lib/jvm/java-1.8-openjdk/jre/bin:/usr/lib/jvm/java-1.8-openjdk/bin

ENV JAVA_VERSION 8u131
ENV JAVA_ALPINE_VERSION 8.131.11-r2

RUN set -x \
	&& apk add --no-cache \
		openjdk8="$JAVA_ALPINE_VERSION" \
&& [ "$JAVA_HOME" = "$(docker-java-home)" ]

#######JENKINS SLAVE########

ENV HOME /home/jenkins
RUN addgroup -g 10000 jenkins
RUN adduser -D -h $HOME -u 10000 -G jenkins jenkins

ARG AGENT_WORKDIR=/home/jenkins/agent

COPY --from=jenkinsci/slave /usr/share/jenkins /usr/share/jenkins

RUN apk add --no-cache py-pip git openssh sudo && pip install docker-compose

USER jenkins
ENV AGENT_WORKDIR=${AGENT_WORKDIR}
RUN mkdir /home/jenkins/.jenkins && mkdir -p ${AGENT_WORKDIR}

VOLUME /home/jenkins/.jenkins
VOLUME ${AGENT_WORKDIR}
WORKDIR /home/jenkins

COPY --from=jenkinsci/jnlp-slave /usr/local/bin/jenkins-slave /usr/local/bin/jenkins-slave

USER root

ENTRYPOINT []
