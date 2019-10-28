fluent-plugin-grafana-loki retry
--------------------------------

再挑戦


## docker run

loki

```
docker run -d -p 3100:3100 \
  --net=loki \
  --name=loki \
  grafana/loki
```

grafana

```
docker run -d -p 3000:3000 \
  --net=loki \
  --name=grafana \
  grafana/grafana
```

fluent-plugin-grafana-loki

```
docker run -d -p 24224:24224 \
  -e LOKI_URL=http://loki:3100 \
  -v ".../lokiprac/step3_retry/conf/:/fluentd/etc/loki/" \
  --net=loki \
  --name=fluentd \
  grafana/fluent-plugin-grafana-loki
```

lokiconf

```
<source>
  @type forward
</source>

<match loki.dev.qwer.**>
  @type loki
  url "#{ENV['LOKI_URL']}"
  username "#{ENV['LOKI_USERNAME']}"
  password "#{ENV['LOKI_PASSWORD']}"
  extra_labels {"env":"dev", "app":"qwer"}
</match>

<match loki.dev.asdf.**>
  @type loki
  url "#{ENV['LOKI_URL']}"
  username "#{ENV['LOKI_USERNAME']}"
  password "#{ENV['LOKI_PASSWORD']}"
  extra_labels {"env":"dev", "app":"asdf"}
</match>

<match loki.prod.qwer.**>
  @type loki
  url "#{ENV['LOKI_URL']}"
  username "#{ENV['LOKI_USERNAME']}"
  password "#{ENV['LOKI_PASSWORD']}"
  extra_labels {"env":"prod", "app":"qwer"}
</match>

<match loki.prod.asdf.**>
  @type loki
  url "#{ENV['LOKI_URL']}"
  username "#{ENV['LOKI_USERNAME']}"
  password "#{ENV['LOKI_PASSWORD']}"
  extra_labels {"env":"prod", "app":"asdf"}
</match>
```

## 確認


fluent-cat

```
$ echo '{"message":"qwer"}' | ./fluent-cat -p 24224 loki.dev.qwer
$ echo '{"message":"asdf"}' | ./fluent-cat -p 24224 loki.dev.asdf
```

コンテナでping

```
docker run --log-driver=fluentd \
    --log-opt fluentd-address=localhost:24224 \
    --log-opt tag=loki.prod.qwer \
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
