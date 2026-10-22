# ----------------------------------
# Pterodactyl Core Dockerfile
# Environment: Java
# Minimum Panel Version: 1.7.0
# ----------------------------------
FROM ubuntu:22.04

ARG TARGETPLATFORM
ARG GRAAL_VERSION=20.0.2
ARG JAVA_VERSION=20

MAINTAINER RikoDEV, <kontakt@riko.dev>

ENV DEBIAN_FRONTEND=noninteractive

# Default to UTF-8 file.encoding
ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'

RUN apt-get update -y \
  && apt-get install -y --no-install-recommends curl ca-certificates openssl git tar sqlite3 fontconfig tzdata locales iproute2 \
  && echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
  && locale-gen en_US.UTF-8 \
  && case ${TARGETPLATFORM} in \
         "linux/amd64")  ARCH=x64  ;; \
         "linux/arm64")  ARCH=aarch64  ;; \
    esac \
  && curl --retry 3 -Lfso /tmp/graalvm.tar.gz https://download.oracle.com/graalvm/${JAVA_VERSION}/archive/graalvm-jdk-${GRAAL_VERSION}_linux-${ARCH}_bin.tar.gz \
  && mkdir -p /opt/java/graalvm \
  && cd /opt/java/graalvm \
  && tar -xf /tmp/graalvm.tar.gz --strip-components=1 \
  && export PATH="/opt/java/graalvm/bin:$PATH" \
  && rm -rf /var/lib/apt/lists/* \
  && rm -rf /tmp/graalvm.tar.gz

ENV JAVA_HOME=/opt/java/graalvm \
    PATH="/opt/java/graalvm/bin:$PATH"

USER root

RUN test -d "$JAVA_HOME/lib/security" && printf '%s\n' \
 '# eMudhra compromised intermediate CAs (2026-10-20)' \
 '42C4ACCD12C4F59597925E1A08A7A90102608543809308D50EC427E118BA1493' \
 '45A883F349AEDFD5F3A649B5498FFB121712BA8B448F3F2A91DBC5A766FCDDBE' \
 '98B896625C0C230ADEEC3EB2CF6AAD5001ECF9156A68644E82374CE5833BC25B' \
 '5F1DED4DD590AF74631290CF073B122A2AAC6A1FA479778A0BD43579BD832699' \
 'E7568FE6E12331F4FD7DE61DA4C7559D0F8EB192A218D4741A3BA75184333838' \
 '579480AEA166290BD3C643589E795FC0D0E54E86505D180F1DFF99EFD3199512' \
 '0164D7CEAE5BB909C272AF151FF44B1177AE9B47D9424B94F1D9558C30934D19' \
 '62B7F9868263F72F6911FC78CEAF72C1979158B7368BF648683BB2AA39B02E72' \
 'CB6E15165AC802E9B3F993F16C25D41F4E81C21EBDE71AE1B012AC00BF9C7C3F' \
 '7485B51C0ED192878FA1D2F948FB529AE1842CC5086C4B67F7BE5B2039DE9489' \
 '432EC25F0D28DE9006D97BF752F17BBB2CD41F669933FCB072D9A1148D0261F3' \
 'FD9D5C2DBBD4995AB69B8D50C439D9527B00529199C2FD01B2174EB58F07021F' \
 '9E39DE2881273ACC225A149C72BA3E0F51424963669AB633EE1DC7DF5DB067B4' \
 'A496E23AC0CAB8155FC9B117B97EAE368EFCBB9041AEC8804D5A308D2D01C2D8' \
 '4B2460A466F95FD40113BCDCCA2CEB29B3B093921DE45474DA7DF48874C630B1' \
 '84112972B8E59D14560AE7E7119654474AAD119990CF3722BFC1E3B91615E1FD' \
 >> "$JAVA_HOME/lib/security/blocked.certs"

# Step 2 - add pterodactyl stuff
RUN useradd -d /home/container -m container

USER        container
ENV         USER=container HOME=/home/container

WORKDIR     /home/container

COPY        ./../entrypoint.sh /entrypoint.sh

CMD         ["/bin/bash", "/entrypoint.sh"]
