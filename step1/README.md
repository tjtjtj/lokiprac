docker-compose でloki を試す

docker-compose up -d

$ docker-compose ps
            Name                           Command               State               Ports
-------------------------------------------------------------------------------------------------------
step1_grafana_1_6c476278265e    /run.sh                          Up      0.0.0.0:3000->3000/tcp
step1_loki_1_3bf5515c8b4e       /usr/bin/loki -config.file ...   Up      0.0.0.0:3100->3100/tcp, 80/tcp
step1_promtail_1_7676d8f2b725   /usr/bin/promtail -config. ...   Up



http://localhost:3000

add datasource

  http://loki:3100


起動した マシンの /var/log が promtail で loki に転送され、grafana で確認できる


掃除

docker-compose down



