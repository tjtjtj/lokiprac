loki-docker-driver
------------------

promtail 使わず loki-docker-driver でログ転送する。


## loki + grafana 起動
docker-compose.yaml は promtail をコメントアウトしている。


```
$ docker-compose up -d
Creating network "step2_loki" with the default driver
Creating step2_loki_1_864f1fffe5e7    ... done
Creating step2_grafana_1_51e05f917092 ... done
```


## loki-docker-driver インストール

インストール

```
$ docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions
latest: Pulling from grafana/loki-docker-driver
7ff4041d336b: Download complete
Digest: sha256:6b5a85811b87539bf8541cae1235029dd3e9436917690fbde44f4e1c4ef8fe25
Status: Downloaded newer image for grafana/loki-docker-driver:latest
Installed plugin grafana/loki-docker-driver:latest
```

## お掃除

$ docker rm zealous_tesla
$ docker-compose down
$ docker plugin disable loki
$ docker plugin rm loki






