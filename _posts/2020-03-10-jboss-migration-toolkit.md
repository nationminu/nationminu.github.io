---
layout: post
title: "Windup Red Hat Application Migration Toolkit Dockerzie "
---

> RED HAT APPLICATION MIGRATION TOOLKIT 는 JBoss 마이그레이션 도구(커뮤니티 windup)로 application 분석을 보다 효율적으로 수행하기 위한 방법입니다. <BR>
> 애플리케이션을 분석하고 코드나 설정등에 대한 수정 내용을 HTML 형식의 보고서로 변환하여 보여준다. <BR>
> Weblogic 에서 사용중인 WAR 어플리케이션을 RHAMT 로 변환하게되면 JBoss 로 변경할 부분에 대한 공수(Story Point)를 산정하여 리포팅한다. <BR>
> CLI 모드, Web Console, IDE Plugin 형태의 바이너리를 제공한다. <BR>
> https://developers.redhat.com/products/rhamt/overview <BR>

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
# windup.sh --input /app/src --output /app/result --source weblogic --target java-ee --sourceMode
{% endhighlight %}

### Make a Yaml for docker-compose 
* docker-compose.yaml

{% highlight yaml %}
---
version: '3'

services:
  windup:
      build: ./windup/ 
      environment:
        - TZ= "Asia/Seoul" 
      volumes:
        - ./webapp/:/app/src
        - ./result/:/app/result
      working_dir: /app
      command: "windup --input /app/src --output /app/result --source weblogic --target java-ee --sourceMode"
...
# docker-compose up --build{% endhighlight %}
