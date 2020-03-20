---
layout: post
title: "wildfly Web Application Server Dockerzie "
---

> Wildfly 는 JBoss Enterpise Application Server 의 커뮤니티 버전이다.
> 가볍고 유연하고 관리가 편리한 오픈소스 웹 어플리케이션 서버이다.
> https://wildfly.org/ <BR>

* **Unparalleled Speed** 
* **Exceptionally Lightweight**
* **Powerful Administration**
* **Supports Latest Standards and Technology**
* **Modular Java**
* **Easily Testable**
* **Based on the Best of Open Source**


#### Make a Dockerfile
* Dockerfile

{% highlight shell %}
FROM openjdk:8

ENV WILDFLY_VERSION 18.0.1.Final

RUN curl -fsSL https://download.jboss.org/wildfly/${WILDFLY_VERSION}/wildfly-${WILDFLY_VERSION}.tar.gz | tar xzf - -C /usr/share \
  && mv /usr/share/wildfly-${WILDFLY_VERSION} /usr/share/wildfly

ENV WILDFLY_HOME /usr/share/wildfly

ADD example.war ${WILDFLY_HOME}/standalone/deployments/
ADD configuration/standalone-full-docker.xml /usr/share/wildfly/standalone/configuration/standalone-full-docker.xml
ADD module.ext /usr/share/wildfly/module.ext
ADD docker-entrypoint.sh /

EXPOSE 8080
EXPOSE 8009
EXPOSE 8443

CMD ["/docker-entrypoint.sh"]
{% endhighlight %}

#### Build Image for windup

{% highlight shell %}
docker build -t wildfly:18 .
{% endhighlight %}

#### Make a Bash Script Executable 
* docker-entrypoint.sh

{% highlight shell %}
#!/usr/bin/env bash

export CONTAINER_IP=$(hostname -I | awk '{print $1}')
export JAVA_OPTS="-Djboss.bind.address=${CONTAINER_IP} -Djgroups.bind_addr=${CONTAINER_IP} -Djboss.bind.address.management=127.0.0.1"
export JAVA_OPTS="${JAVA_OPTS} -Dorg.jboss.as.logging.per-deployment=false -Dspring.profiles.active=PRD"
export JBOSS_MODULEPATH=${WILDFLY_HOME}/modules:${WILDFLY_HOME}/module.ext

${WILDFLY_HOME}/bin/standalone.sh -c standalone-full-docker.xml
{% endhighlight %}

### Make a Yaml for docker-compose 
* docker-compose.yaml

{% highlight yaml %}
---
version: '3'

services:
  wildfly:
    build: ./wildfly/
    ports:
        - "8080:8080"
        - "8009:8009"
        - "8443:8443"
    environment:
        TZ: "Asia/Seoul"
    volumes: 
        - ${pwd}/wildfly/module.ext:/usr/share/wildfly/module.ext:rw 
...
# docker-compose up --build
{% endhighlight %}
