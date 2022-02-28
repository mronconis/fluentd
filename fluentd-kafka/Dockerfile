FROM fluent/fluentd:v1.11.1-1.0

#----------
USER root

RUN apk update && apk add \
        ruby-dev \
        build-base \
        curl \
        tcpdump

RUN ["gem", "install", "fluent-plugin-record-modifier", "--no-document"]
RUN ["gem", "install", "fluent-plugin-concat", "--no-document"]
RUN ["gem", "install", "fluent-plugin-multiline-parser", "--no-document"]
RUN ["gem", "install", "thread_safe", "--no-document"]
RUN ["gem", "install", "fluent-plugin-multi-format-parser", "--no-document"]
RUN ["gem", "install", "fluent-plugin-kafka", "--no-document"]


RUN apk del build-base

RUN addgroup fluent root

WORKDIR /fluentd

#----------
USER fluent

RUN mkdir /tmp/access_logs