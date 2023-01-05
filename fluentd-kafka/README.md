# Sample usage of kafka2 plugin on fluentd

The project contains a basic configuration of fluentd to read from files in csv format, enrich and transform the payload in json format and write on topic kafka.

Full documentation at the following link https://docs.fluentd.org/output/kafka

## Install

````
fluent-gem install fluent-plugin-kafka
````

## Configure
For the full configuration see [fluentd.conf](https://github.com/mronconis/fluentd/tree/main/fluentd-kafka/fluentd.conf)

````
  ...output omit...
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
  ...output omit...
````

## Build
````
docker-compose build
````

## Run
````
docker-compose up
````

## Test
````
export SAMPLE=3
for i in $(seq $SAMPLE); do
  time=`date +%Y-%m-%dT%T+00:00`
  echo "$i,/foo/bar?id=$i,GET,Mario,Rossi,mrossi@acme.org,$time" >> ${PWD}/test/access_logs.csv
  sleep 2;
done

1,/foo/bar?id=1,GET,Mario,Rossi,mrossi@acme.org,2023-01-05T12:11:10+00:00
2,/foo/bar?id=2,GET,Mario,Rossi,mrossi@acme.org,2023-01-05T12:11:12+00:00
3,/foo/bar?id=3,GET,Mario,Rossi,mrossi@acme.org,2023-01-05T12:11:14+00:00
````

## Verify
````
# list topics
docker exec -it fluentd-kafka-kafka-1 /opt/kafka/bin/kafka-topics.sh \
    --bootstrap-server localhost:9092 \
    --list

access-logs-topic
````

````
# read data from topic
docker exec -it fluentd-kafka-kafka-1 /opt/kafka/bin/kafka-console-consumer.sh \
    --bootstrap-server localhost:9092 \
    --topic access-logs-topic \
    --from-beginning

{"value":{"id":"1","url":"/foo/bar?id=1","method":"GET","name":"Mario","surname":"Rossi","email":"mrossi@acme.org"},"sent-time":"2023-01-05 12:11:10 +0000","topic":"access-logs-topic"}
{"value":{"id":"2","url":"/foo/bar?id=2","method":"GET","name":"Mario","surname":"Rossi","email":"mrossi@acme.org"},"sent-time":"2023-01-05 12:11:12 +0000","topic":"access-logs-topic"}
{"value":{"id":"3","url":"/foo/bar?id=3","method":"GET","name":"Mario","surname":"Rossi","email":"mrossi@acme.org"},"sent-time":"2023-01-05 12:11:14 +0000","topic":"access-logs-topic"}
````

## Metrics
````
curl http://localhost:24231/metrics

# TYPE fluentd_output_status_num_records_total counter
# HELP fluentd_output_status_num_records_total The total number of outgoing records
# TYPE fluentd_input_status_num_records_total counter
# HELP fluentd_input_status_num_records_total The total number of incoming records
# TYPE fluentd_output_status_buffer_total_bytes gauge
# HELP fluentd_output_status_buffer_total_bytes Current total size of stage and queue buffers.
fluentd_output_status_buffer_total_bytes{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 320.0
# TYPE fluentd_output_status_buffer_stage_length gauge
# HELP fluentd_output_status_buffer_stage_length Current length of stage buffers.
fluentd_output_status_buffer_stage_length{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 1.0
# TYPE fluentd_output_status_buffer_stage_byte_size gauge
# HELP fluentd_output_status_buffer_stage_byte_size Current total size of stage buffers.
fluentd_output_status_buffer_stage_byte_size{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 320.0
# TYPE fluentd_output_status_buffer_queue_length gauge
# HELP fluentd_output_status_buffer_queue_length Current length of queue buffers.
fluentd_output_status_buffer_queue_length{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 0.0
# TYPE fluentd_output_status_queue_byte_size gauge
# HELP fluentd_output_status_queue_byte_size Current total size of queue buffers.
fluentd_output_status_queue_byte_size{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 0.0
# TYPE fluentd_output_status_buffer_available_space_ratio gauge
# HELP fluentd_output_status_buffer_available_space_ratio Ratio of available space in buffer.
fluentd_output_status_buffer_available_space_ratio{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 100.0
# TYPE fluentd_output_status_buffer_newest_timekey gauge
# HELP fluentd_output_status_buffer_newest_timekey Newest timekey in buffer.
# TYPE fluentd_output_status_buffer_oldest_timekey gauge
# HELP fluentd_output_status_buffer_oldest_timekey Oldest timekey in buffer.
# TYPE fluentd_output_status_retry_count gauge
# HELP fluentd_output_status_retry_count Current retry counts.
fluentd_output_status_retry_count{hostname="c60446e28913",plugin_id="object:8fc",type="stdout"} 0.0
fluentd_output_status_retry_count{hostname="c60446e28913",plugin_id="ignore_fluent_logs",type="null"} 0.0
fluentd_output_status_retry_count{hostname="c60446e28913",plugin_id="object:884",type="relabel"} 0.0
fluentd_output_status_retry_count{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 0.0
fluentd_output_status_retry_count{hostname="c60446e28913",plugin_id="object:8c0",type="copy"} 0.0
fluentd_output_status_retry_count{hostname="c60446e28913",plugin_id="object:8d4",type="prometheus"} 0.0
# TYPE fluentd_output_status_num_errors gauge
# HELP fluentd_output_status_num_errors Current number of errors.
fluentd_output_status_num_errors{hostname="c60446e28913",plugin_id="object:8fc",type="stdout"} 0.0
fluentd_output_status_num_errors{hostname="c60446e28913",plugin_id="ignore_fluent_logs",type="null"} 0.0
fluentd_output_status_num_errors{hostname="c60446e28913",plugin_id="object:884",type="relabel"} 0.0
fluentd_output_status_num_errors{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 0.0
fluentd_output_status_num_errors{hostname="c60446e28913",plugin_id="object:8c0",type="copy"} 0.0
fluentd_output_status_num_errors{hostname="c60446e28913",plugin_id="object:8d4",type="prometheus"} 0.0
# TYPE fluentd_output_status_emit_count gauge
# HELP fluentd_output_status_emit_count Current emit counts.
fluentd_output_status_emit_count{hostname="c60446e28913",plugin_id="object:8fc",type="stdout"} 0.0
fluentd_output_status_emit_count{hostname="c60446e28913",plugin_id="ignore_fluent_logs",type="null"} 29.0
fluentd_output_status_emit_count{hostname="c60446e28913",plugin_id="object:884",type="relabel"} 5.0
fluentd_output_status_emit_count{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 5.0
fluentd_output_status_emit_count{hostname="c60446e28913",plugin_id="object:8d4",type="prometheus"} 0.0
# TYPE fluentd_output_status_emit_records gauge
# HELP fluentd_output_status_emit_records Current emit records.
fluentd_output_status_emit_records{hostname="c60446e28913",plugin_id="object:8fc",type="stdout"} 0.0
fluentd_output_status_emit_records{hostname="c60446e28913",plugin_id="ignore_fluent_logs",type="null"} 29.0
fluentd_output_status_emit_records{hostname="c60446e28913",plugin_id="object:884",type="relabel"} 14.0
fluentd_output_status_emit_records{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 14.0
fluentd_output_status_emit_records{hostname="c60446e28913",plugin_id="object:8d4",type="prometheus"} 0.0
# TYPE fluentd_output_status_write_count gauge
# HELP fluentd_output_status_write_count Current write counts.
fluentd_output_status_write_count{hostname="c60446e28913",plugin_id="object:8fc",type="stdout"} 0.0
fluentd_output_status_write_count{hostname="c60446e28913",plugin_id="ignore_fluent_logs",type="null"} 0.0
fluentd_output_status_write_count{hostname="c60446e28913",plugin_id="object:884",type="relabel"} 0.0
fluentd_output_status_write_count{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 1.0
fluentd_output_status_write_count{hostname="c60446e28913",plugin_id="object:8d4",type="prometheus"} 0.0
# TYPE fluentd_output_status_rollback_count gauge
# HELP fluentd_output_status_rollback_count Current rollback counts.
fluentd_output_status_rollback_count{hostname="c60446e28913",plugin_id="object:8fc",type="stdout"} 0.0
fluentd_output_status_rollback_count{hostname="c60446e28913",plugin_id="ignore_fluent_logs",type="null"} 0.0
fluentd_output_status_rollback_count{hostname="c60446e28913",plugin_id="object:884",type="relabel"} 0.0
fluentd_output_status_rollback_count{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 0.0
fluentd_output_status_rollback_count{hostname="c60446e28913",plugin_id="object:8d4",type="prometheus"} 0.0
# TYPE fluentd_output_status_flush_time_count gauge
# HELP fluentd_output_status_flush_time_count Total flush time.
fluentd_output_status_flush_time_count{hostname="c60446e28913",plugin_id="object:8fc",type="stdout"} 0.0
fluentd_output_status_flush_time_count{hostname="c60446e28913",plugin_id="ignore_fluent_logs",type="null"} 0.0
fluentd_output_status_flush_time_count{hostname="c60446e28913",plugin_id="object:884",type="relabel"} 0.0
fluentd_output_status_flush_time_count{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 86.0
fluentd_output_status_flush_time_count{hostname="c60446e28913",plugin_id="object:8d4",type="prometheus"} 0.0
# TYPE fluentd_output_status_slow_flush_count gauge
# HELP fluentd_output_status_slow_flush_count Current slow flush counts.
fluentd_output_status_slow_flush_count{hostname="c60446e28913",plugin_id="object:8fc",type="stdout"} 0.0
fluentd_output_status_slow_flush_count{hostname="c60446e28913",plugin_id="ignore_fluent_logs",type="null"} 0.0
fluentd_output_status_slow_flush_count{hostname="c60446e28913",plugin_id="object:884",type="relabel"} 0.0
fluentd_output_status_slow_flush_count{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 0.0
fluentd_output_status_slow_flush_count{hostname="c60446e28913",plugin_id="object:8d4",type="prometheus"} 0.0
# TYPE fluentd_output_status_retry_wait gauge
# HELP fluentd_output_status_retry_wait Current retry wait
fluentd_output_status_retry_wait{hostname="c60446e28913",plugin_id="object:8fc",type="stdout"} 0.0
fluentd_output_status_retry_wait{hostname="c60446e28913",plugin_id="ignore_fluent_logs",type="null"} 0.0
fluentd_output_status_retry_wait{hostname="c60446e28913",plugin_id="object:884",type="relabel"} 0.0
fluentd_output_status_retry_wait{hostname="c60446e28913",plugin_id="object:898",type="kafka2"} 0.0
fluentd_output_status_retry_wait{hostname="c60446e28913",plugin_id="object:8d4",type="prometheus"} 0.0
````
