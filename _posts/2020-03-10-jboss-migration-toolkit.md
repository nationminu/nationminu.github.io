---
layout: post
title: "Windup Red Hat Application Migration Toolkit Dockerize"
---

> RED HAT APPLICATION MIGRATION TOOLKIT(이하 RHAMT) 는 JBoss 마이그레이션 도구(커뮤니티 windup)로 application 분석을 보다 효율적으로 수행하기 위한 방법입니다.애플리케이션을 분석하고 코드나 설정등에 대한 수정 내용을 HTML 형식의 보고서로 변환하여 보여줍니다.
>
> RHAMT 은 사용중인 WAR 어플리케이션을 분석하여 클라우드 네이티브, Java EE 또는 JBoss EAP 로 효율적으로 마이그레이션 할 수 있게 도와줍니다.
> CLI 모드, Web Console, IDE Plugin 형태의 바이너리를 제공합니다.
>
> [RHAMT](https://developers.redhat.com/products/rhamt/overview)

#### Make a Dockerfile
* Dockerfile

{% highlight shell %}
FROM openjdk:8-jdk

ENV WINDUP_VERSION 4.3.0

# download migrationtoolkit -> https://developers.redhat.com/products/rhamt/download
ADD migrationtoolkit-rhamt-cli-${WINDUP_VERSION}-offline.zip /usr/share

RUN cd /usr/share/ && unzip migrationtoolkit-rhamt-cli-${WINDUP_VERSION}-offline.zip\
  && mv /usr/share/rhamt-cli-${WINDUP_VERSION}.Final /usr/share/windup \
  && cd /usr/share/windup/  \
  && ln -s /usr/share/windup/bin/rhamt-cli /usr/bin/windup

ENV WINDUP_HOME /usr/share/windup

CMD ["windup"]
{% endhighlight %}


#### Build Image for windup

{% highlight shell %}
docker build -t windup:4 .
{% endhighlight %}

#### Make a Bash Script Executable 
* windup.sh 

{% highlight shell %}
#!/usr/bin/env bash
docker run -v "$PWD/webapp:/app/src" -v "$PWD/result:/app/result" -w /app -it windup:4 $@

# execute docker for windup
# windup.sh --input /app/src/simple-sample-app.ear --output /app/result --source weblogic --target eap:7 --packages com.acme

# simple app
# wget https://github.com/windup/windup-sample-apps/raw/simple-sample-weblogic-logmanager/sample-apps/simple-sample/prebuilt/simple-sample-app.ear
{% endhighlight %}

### Make a Yaml for docker-compose 
* docker-compose.yaml

{% highlight yaml %}
---
version: '3'

services:
  windup:
      build: ./
      environment:
        - TZ= "Asia/Seoul"
      volumes:
        - ./webapp/:/app/src
        - ./result/:/app/result
      working_dir: /app
      command: "windup --input /app/src/simple-sample-app.ear --output /app/result --source weblogic --target eap:7 --packages com.acme"
...
# docker-compose up --build
{% endhighlight %}
