# grafana-docker-stack

For deploying Grafana, Prometheus and Node Exporter, make these steps:
1. Deploy stack
```
git clone https://github.com/digitalstudium/grafana-docker-stack.git
docker stack deploy -c grafana-docker-stack/docker-compose.yml monitoring
```
2. Add prometheus datasource with address `http://prometheus:9090` to grafana
3. Add these three lines to the bottom of `/var/lib/docker/volumes/monitoring_prom-configs/_data/prometheus.yml` file, to `scrape_configs:` section:
```
  - job_name: 'node-exporter'

    static_configs:
      - targets: ['node-exporter:9100']
```
4. Reload prometheus config via this command:
```
docker ps | grep prometheus | awk '{print $1}' | xargs docker kill -s SIGHUP
```
5. Import this dashboard: https://grafana.com/grafana/dashboards/1860 to grafana.

That's it!

If you want to add more servers to prometheus, make these steps:
1. Install node-exporter to each of these servers via these commands:
```
git clone https://github.com/digitalstudium/grafana-docker-stack.git
docker stack deploy -c grafana-docker-stack/node-exporter.yml node-exporter
```
2. Add these servers to `/var/lib/docker/volumes/monitoring_prom-configs/_data/prometheus.yml` file to `- targets: ['node-exporter:9100']` list of `- job_name: 'node-exporter'` section, like `- targets: ['node-exporter:9100', 'server1:9100', 'server2:9100', '...']`
3. Reload prometheus config via this command:
```
docker ps | grep prometheus | awk '{print $1}' | xargs docker kill -s SIGHUP

