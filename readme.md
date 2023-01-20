### Dependencies

- docker
- docker compose

### Getting started

```bash
# Install docker and docker-compose
apt-get update && apt install docker.io docker-compose

mv prometheus.yml prometheus.prod.yml
docker-compose up -d
```