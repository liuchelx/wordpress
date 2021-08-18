FROM jenkins/inbound-agent:alpine as jnlp

FROM python:alpine

RUN apk -U --no-cache add curl openjdk8-jre git gcc g++ make openssh libffi-dev openssl-dev docker-cli; \
    pip3 install virtualenv

COPY --from=jnlp /usr/local/bin/jenkins-agent /usr/local/bin/jenkins-agent
COPY --from=jnlp /usr/share/jenkins/agent.jar /usr/share/jenkins/agent.jar

ENV JAVA_HOME=/usr/lib/jvm/java-1.8-openjdk
ENV SONAR_RUNNER_VERSION=4.5.0.2216
ENV SONAR_RUNNER_HOME=/usr/bin/sonar-scanner-$SONAR_RUNNER_VERSION-linux

RUN curl -k -v -L https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-$SONAR_RUNNER_VERSION-linux.zip | unzip -; \
    mv sonar-scanner-$SONAR_RUNNER_VERSION-linux /usr/bin; \
    chmod +x /usr/bin/sonar-scanner-$SONAR_RUNNER_VERSION-linux/bin/sonar-scanner; \
    ln -s /usr/bin/sonar-scanner-%SONAR_RUNNER_VERSION-linux/bin/sonar-scanner /usr/bin/sonar-scanner

RUN mv /usr/bin/sonar-scanner-$SONAR_RUNNER_VERSION-linux/jre/bin/java /usr/bin/sonar-scanner-$SONAR_RUNNER_VERSION-linux/jre/bin/java.bak; \
    ln -s /usr/bin/java /usr/bin/sonar-scanner-$SONAR_RUNNER_VERSION-linux/jre/bin/java

ENTRYPOINT ["/usr/local/bin/jenkins-agent"]
