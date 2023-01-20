### Dependencies

- docker
- docker compose (on the monitoring node)

### Setup monitoring node

```bash
# 0. clone this project and cd into it:
git clone https://github.com/mehran-prs/monitoring.git && cd monitoring

# 1. Install docker and docker-compose
apt-get update && apt install docker.io docker-compose

# 2. create config file (we'll edit prometheus.prod.yml as config file)
cp prometheus.yml prometheus.prod.yml

# 3. Put a password at `prometheus.prod.yaml` file in the basic_auth -> password section (we will set this password on each node exporter).

# 4. run the monitoring stack
docker-compose up -d

# 4. browse {your_node_ip}:3000 on your browser, login (username: admin, password: admin) and the it'll ask you to change your password.
```

### Add a node (to collect its metrics)

```bash

# 0. clone this project and cd into it:
git clone https://github.com/mehran-prs/monitoring.git && cd monitoring

# 1. Run the following command and when it asks you enter a password, enter the password 
# that you have configured on the prometheus config file at step 3 of setting up monitoring stack.
# This command will generates hashed value of that password using bcrypt hashing algorithm.
htpasswd -nBC 10 "" | tr -d ':\n' # This will ask you a password and prints hashed password.

# 2. Create config file
cp node-exporter.yml node-exporter.prod.yml

# 3. Put the hashed password as value of the `prometheus` key in the `node-exporter.prod.yml` config file.

# 4. Run the node exporter 
docker run -d \
  --net="host" \
  --pid="host" \
  -v "/:/host:ro,rslave" \
  -v "$(pwd)/web-config.prod.yml:/etc/node-exporter.yml" \
  prom/node-exporter:v1.5.0 \
  --path.rootfs=/host \
  --web.config.file=/etc/node-exporter.yml
  
# 5. Add the `{node's public ip}:9100`(e.g., 123.123.123.123:9100) to the `static_configs -> targets` array of "vpn" job 
# in the `prometheus.prod.yaml` config file on the monitoring node. For example it should be something like this

  - job_name: 'vpn'
    basic_auth:
      username: 'prometheus'
      password: 'my_secure_password'
    static_configs:
      - targets: ['123.123.123.123:9100']

# 6. Restart prometheus on the monitoring node
docker-compose restart prometheus
```
