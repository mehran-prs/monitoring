```bash
cp -p -r ./provisioning  /etc/grafana/provisioning
cp prometheus.yml prometheus.prod.yml
docker-compose up -d
```