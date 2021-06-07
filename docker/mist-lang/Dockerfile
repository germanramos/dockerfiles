FROM demisto/python3:3.9.4.18682 as base

RUN apk update && \
    apk upgrade

FROM base as build

RUN apk add --no-cache build-base gcc musl-dev python3-dev libffi-dev libxml2-dev libxslt-dev
RUN python -OO -m pip install --no-cache-dir -U pip && \
    python -OO -m pip wheel --no-cache-dir --wheel-dir=/root/wheels lxml hiredis multidict yarl

FROM base

# lxml binary dependencies
COPY --from=build /usr/lib/libxslt.so.1 /usr/lib/libxslt.so.1
COPY --from=build /usr/lib/libexslt.so.0 /usr/lib/libexslt.so.0
COPY --from=build /usr/lib/libxml2.so.2 /usr/lib/libxml2.so.2
COPY --from=build /usr/lib/libgcrypt.so.20 /usr/lib/libgcrypt.so.20
COPY --from=build /usr/lib/libgpg-error.so.0 /usr/lib/libgpg-error.so.0
COPY --from=build /root/wheels /root/wheels
RUN python -OO -m pip install --no-cache --no-index --find-links=/root/wheel/* /root/wheels/*

# Python requirements
RUN python -OO -m pip install --no-cache bandit festin dnsrecon mist-lang bandit awscli

# Packages requirements
RUN apk add bash wget git curl nmap openjdk8-jre

# gitleaks
RUN curl -L https://github.com/zricethezav/gitleaks/releases/download/v6.1.2/gitleaks-linux-amd64 --output /usr/bin/gitleaks && chmod a+x /usr/bin/gitleaks
RUN curl --show-error --location "https://downloads.apache.org/kafka/2.8.0/kafka_2.13-2.8.0.tgz" | tar -xzf - -C /usr/local

# kafka
RUN cd /usr/local/kafka_2.13-2.8.0/bin && ln -s kafka-console-consumer.sh kafka-console-consumer && ln -s kafka-console-producer.sh kafka-console-producer
ENV PATH="/usr/local/kafka_2.13-2.8.0/bin:${PATH}"
