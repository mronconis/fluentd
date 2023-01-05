
# Sample usage of grep plugin on fluentd

Full documentation at the following link https://docs.fluentd.org/filter/grep

## Configure 
For the full configuration see [fluentd.conf](https://github.com/mronconis/fluentd/tree/main/fluentd-grep/fluentd.conf)

````
  ...output omit...
  <filter sample.test>
    @type grep

    <and>
      <regexp>
        key message
        pattern /foo/
      </regexp>
      <regexp>
        key code
        pattern /^[0-9]+$/
      </regexp>
    </and>

    <exclude>
      key message
      pattern /^[0-9]+/
    </exclude>
  </filter>
  ...output omit...
````

## Build
````
make build
````

## Run
````
make run
````

## Test
````
curl -X POST -d 'json={"message":"foo bar", "code": "123"}' http://localhost:9880/sample.test
curl -X POST -d 'json={"message":"foo bar", "code": "abc123"}' http://localhost:9880/sample.test
curl -X POST -d 'json={"message":"123 foo bar", "code": "123"}' http://localhost:9880/sample.test
````

## Result
````
... output omitt...
2022-02-28 09:43:30 +0000 [info]: #0 starting fluentd worker pid=18 ppid=8 worker=0
2022-02-28 09:43:30 +0000 [info]: #0 fluentd worker is now running worker=0
2022-02-28 09:43:36.277860100 +0000 sample.test: {"message":"foo bar","code":"123","topic":"log-audit-topic"}
````
