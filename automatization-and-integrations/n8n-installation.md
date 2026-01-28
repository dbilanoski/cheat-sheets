# n8n Notes

n8n is a **self-hosted workflow automation tool** that allows you to connect APIs, automate tasks, and build custom workflows. Lightweight, runs in Docker, and suitable for private or public use behind a secure tunnel.

- Run as private instance first, then expose via secure HTTPS/Cloudflare if needed  
- Use Docker volumes for persistent workflows and credentials  

## Deploying n8n (Docker Compose)

- Default port: `5678`
- Recommended: **never expose raw to the public**; use reverse proxy or Cloudflare Tunnel
- Persistent volumes required for workflows, credentials, and runtime config

**Minimal `docker-compose.yml`**

```yaml
version: "3.8"

services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: unless-stopped
    ports:
      - "5678:5678"   # optional for local testing
    environment:
      - TZ=Europe/Zagreb
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=SuperSecurePasswordHere
      - N8N_HOST=n8n.local       # placeholder; update if using public hostname
      - N8N_PROTOCOL=http
      - N8N_PORT=5678
      - N8N_SECURE_COOKIE=false  # disable for local testing; enable with HTTPS
    user: "1000:1000"            # matches host folder permissions
    volumes:
      - /data/appdata/n8n:/home/node/.n8n 
        
```

## Installation Steps

1. Create Docker folders:
   ```bash
   sudo mkdir -p /srv/docker/compose/n8n
	sudo mkdir -p /data/appdata/n8n
	sudo chown -R 1000:1000 /data/appdata/n8n
	sudo chmod -R 700 /data/appdata/n8n
   ```
   
2. Place `docker-compose.yml` in `/srv/docker/compose/n8n`
   
3. Start container:
   ```bash
   cd /srv/docker/compose/n8n
	docker compose up -d
	docker compose ps
	docker logs -f n8n
   ```
   
4. Access locally: `http://<server-ip>:5678` and login with basic auth

Optionally, if fits your environment:

- Add to **Uptime Kuma** for monitoring:
	- Name: `n8n`
	- URL: `http://<server-ip>:5678`
	- Type: HTTP, optionally with basic auth
	  
- Optional Cloudflare Tunnel for public access:
	- Map hostname, e.g., `n8n.atlastname.win`
	- Keep **basic auth enabled**
	- Optionally enable **Cloudflare Access** for zero-trust security


## Best Practices

- **Persistent volume** ensures workflows & credentials survive restarts
- **Backups**: 
  `tar -czf ~/n8n-backup-$(date +%F).tar.gz /data/appdata/n8n`
- **Secure access**: use HTTPS / Cloudflare Tunnel for public exposure
- **Resource usage**: light (~200–300 MB RAM idle), safe alongside other services
- **Python warning** is normal; ignore unless debugging Python tasks

## References

1. [n8n official docs](https://docs.n8n.io/)
2. [Docker hub page](https://hub.docker.com/r/n8nio/n8n)