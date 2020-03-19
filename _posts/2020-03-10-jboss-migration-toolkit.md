---
layout: post
title: "Windup Red Hat Application Migration Toolkit Dockerzie "
---

#### Dockerfile
```
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
```
docker build -t windup:4 .
```

#### Make a Bash Script Executable (windup.sh)
```
#!/usr/bin/env bash
docker run -v "$PWD/webapp:/app/src" -v "$PWD/result:/app/result" -w /app -it windup:4 $@

# execute docker for windup
# windup.sh --input /app/src --output /app/result --source weblogic --target java-ee --sourceMode
```
