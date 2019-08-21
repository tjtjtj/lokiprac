fluent-plugin-grafana-loki
--------------------------

なんか成功しない


## プラグインインストール


インストール前確認

```
$ /opt/td-agent/embedded/bin/fluent-gem list | grep loki
```


プラグインインストール

```
$ sudo /opt/td-agent/embedded/bin/fluent-gem install fluent-plugin-grafana-loki
Fetching: fluent-plugin-grafana-loki-1.0.2.gem (100%)
Successfully installed fluent-plugin-grafana-loki-1.0.2
Parsing documentation for fluent-plugin-grafana-loki-1.0.2
Installing ri documentation for fluent-plugin-grafana-loki-1.0.2
Done installing documentation for fluent-plugin-grafana-loki after 0 seconds
1 gem installed
```

インストール後確認

```
$ /opt/td-agent/embedded/bin/fluent-gem list | grep loki
fluent-plugin-grafana-loki (1.0.2)
```

## 雑に設定し、動作確認

/etc/td-agent/td-agent.conf

```
<match loki.test.**>
  @type loki
  url "http://localhost:3100"
  extra_labels {"app":"loki.test.asdf","asdf":"qwer"}
  flush_interval 10s
  flush_at_shutdown true
  buffer_chunk_limit 1m
</match>
```

$ sudo systemctl reload td-agent
$ curl -X POST -d 'json={"json":"message"}' http://localhost:8888/loki.test.zzz


## fluentd  log driver で送信

```
docker run --log-driver=fluentd \
    --log-opt fluentd-address=localhost:8888 \
    --log-opt tag=loki.test.ping \
    alpine:3.9 ping localhost

PING localhost (127.0.0.1): 56 data bytes
64 bytes from 127.0.0.1: seq=0 ttl=64 time=0.116 ms
64 bytes from 127.0.0.1: seq=1 ttl=64 time=0.103 ms
64 bytes from 127.0.0.1: seq=2 ttl=64 time=0.086 ms
64 bytes from 127.0.0.1: seq=3 ttl=64 time=0.108 ms
64 bytes from 127.0.0.1: seq=4 ttl=64 time=0.110 ms
64 bytes from 127.0.0.1: seq=5 ttl=64 time=0.096 ms
64 bytes from 127.0.0.1: seq=6 ttl=64 time=0.115 ms
^C
--- localhost ping statistics ---
7 packets transmitted, 7 packets received, 0% packet loss
round-trip min/avg/max = 0.086/0.104/0.116 ms
```

```
  2019-08-20 20:05:16 +0900 [warn]: #0 /opt/td-agent/embedded/lib/ruby/gems/2.4.0/gems/fluentd-1.4.2/lib/fluent/plugin_helper/thread.rb:78:in `block in thread_create'
2019-08-20 20:05:19 +0900 [warn]: #0 [input_http] unexpected error error="Could not parse data entirely (0 != 420)"
  2019-08-20 20:05:19 +0900 [warn]: #0 suppressed same stacktrace
2019-08-20 20:05:22 +0900 [warn]: #0 [input_http] unexpected error error="Could not parse data entirely (0 != 420)"
  2019-08-20 20:05:22 +0900 [warn]: #0 suppressed same stacktrace
2019-08-20 20:05:22 +0900 [warn]: #0 [input_http] unexpected error error="Could not parse data entirely (0 != 612)"
  2019-08-20 20:05:22 +0900 [warn]: #0 suppressed same stacktrace
```
