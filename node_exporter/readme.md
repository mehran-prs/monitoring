### Getting started
```bash
# 1. Create config file
cp web-config.yml web-config.prod.yml

# 2. Generate a password
htpasswd -nBC 10 "" | tr -d ':\n'

# 3. Put the generated password as value of the `prometheus` key in the config file.

# 4. Run node exporter 
docker run -d \
  --net="host" \
  --pid="host" \
  -v "/:/host:ro,rslave" \
  -v "$(pwd)/web-config.prod.yml:/etc/node-exporter.yml" \
  prom/node-exporter:v1.5.0 \
  --path.rootfs=/host \
  --web.config.file=/etc/node-exporter.yml
```