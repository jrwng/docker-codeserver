FROM ubuntu:20.04

RUN set -eux \
    && apt-get update \
    && DEBIAN_FRONTEND="noninteractive" apt-get -y install --no-install-recommends \
        ca-certificates \
        locales \
        tzdata \
        wget \
        curl \
        git \
        openjdk-11-jdk \
        openjdk-11-source \
        openssh-server \
    && rm -rf /var/lib/apt/lists/* \
    && mkdir /var/run/sshd

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]