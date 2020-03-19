---
layout: post
title: "Windup Red Hat Application Migration Toolkit Dockerzie "
---

> RED HAT APPLICATION MIGRATION TOOLKIT 는 JBoss 마이그레이션 도구(커뮤니티 windup)
> 애플리케이션을 분석하고 코드나 설정등에 대한 수정 내용을 HTML 형식의 보고서로 변환하여 보여준다.
> Weblogic 에서 사용중인 WAR 어플리케이션을 RHAMT 로 변환하게되면 JBoss 로 변경할 부분에 대한 공수(Story Point)를 산정하여 리포팅한다. 
> CLI 모드, Web Console, IDE Plugin 형태의 바이너리를 제공한다.
> https://developers.redhat.com/products/rhamt/overview

#### Make a Dockerfile
* Dockerfile

```bash
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
```

#### Build Image for windup

```bash
docker build -t windup:4 .
```

#### Make a Bash Script Executable 
* windup.sh 

```bash
#!/usr/bin/env bash
docker run -v "$PWD/webapp:/app/src" -v "$PWD/result:/app/result" -w /app -it windup:4 $@

# execute docker for windup
# windup.sh --input /app/src --output /app/result --source weblogic --target java-ee --sourceMode
```

### Make a Yaml for docker-compose 
* docker-compose.yaml

```yaml
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
# docker-compose up --build
```
