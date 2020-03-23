---
layout: post
title: "Wildfly Web Application Server Dockerize"
categories: dockerize
---

> 와일드플라이(WildFly, 이전 이름: JBoss)는 자바를 기반으로 하는 오픈 소스 미들웨어의 총칭이다. 대표적으로 Java EE 스펙을 지원하는 제이보스 애플리케이션 서버가 있다. 현재 40개 이상의 다양한 프로젝트가 있으며, Jboss.org 커뮤니티에 의해 개발 및 운영되고 있다.
> 
> 제이보스는 각 프로젝트의 핵심 개발자를 Jboss Inc.의 직원으로 고용하고 있으므로 오픈 소스의 프로젝트이면서 직원으로 고용하면서 제품 개발을 계속하는 독특한 형태를 취하고 있다. Jboss Inc.는 소프트웨어를 프리 라이선스로 제공하면서 지원 서비스를 판매하여 수익을 올리고 있다.
> 
> 2006년에는 상용 리눅스 밴더인 레드햇에서 인수하여 제이보스 프로젝트를 운영하고 있다. 2007년부터는 레드햇은 각종 컴포넌트의 제공 및 보증 및 통합 품질 테스트를 완료한 제이보스 소프트웨어를 제이보스 엔터프라이즈 미들웨어로 제공하고 있다.
> 
> 2014년 11월 20일 레드햇은 기존 제이보스의 이름을 와일드플라이(WildFly)로 변경했다.
>
> [wikipedia](https://ko.wikipedia.org/wiki/%EC%99%80%EC%9D%BC%EB%93%9C%ED%94%8C%EB%9D%BC%EC%9D%B4)

#### Make a Dockerfile
* Dockerfile

{% highlight shell %}
FROM openjdk:8-jdk

ENV WILDFLY_VERSION 18.0.1.Final

RUN curl -fsSL https://download.jboss.org/wildfly/${WILDFLY_VERSION}/wildfly-${WILDFLY_VERSION}.tar.gz | tar xzf - -C /usr/share \
  && mv /usr/share/wildfly-${WILDFLY_VERSION} /usr/share/wildfly

ENV WILDFLY_HOME /usr/share/wildfly

ADD webapp/example.war ${WILDFLY_HOME}/standalone/deployments/
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
    build: ./
    ports:
        - "8080:8080"
        - "8009:8009"
        - "8443:8443"
    environment:
        TZ: "Asia/Seoul"
    volumes:
        - ${pwd}/module.ext:/usr/share/wildfly/module.ext:rw
...
# docker-compose up --build
{% endhighlight %}
