
# Sample usage of kafka plugin on fluentd

Full documentation at the following link https://docs.fluentd.org/output/kafka

## Install

````
fluent-gem install fluent-plugin-kafka
````

## Configure (full configuration fluentd.conf)
````
  <match pattern>
    @type kafka2

    # list of seed brokers
    brokers <broker1_host>:<broker1_port>,<broker2_host>:<broker2_port>
    use_event_time true

    # buffer settings
    <buffer topic>
      @type file
      path /var/log/td-agent/buffer/td
      flush_interval 3s
    </buffer>

    # data type settings
    <format>
      @type json
    </format>

    # topic settings
    topic_key topic
    default_topic messages

    # producer settings
    required_acks -1
    compression_codec gzip
  </match>
````

## Build fluentd
````
make build
````

## Run fluentd
````
make run
````

## Send test data
````
echo "1,/foo/bar,POST,Mario,Rossi,mrossi@acme.org,2023-01-3T14:57:59+00:00" >> ${PWD}/test/access_logs.csv
````

## Result
````
... output omitt...
2023-01-03 14:57:59.000000000 +0000 access_logs: {"value":{"id":"1","name":"Mario","surname":"Rossi","email":"mrossi@acme.org"},"sent-time":"2023-01-03 14:57:59 +0000"}
````
