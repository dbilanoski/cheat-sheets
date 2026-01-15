# Cloudflare's Zero Trust Tuneling Note


Cloudflare is a global edge network that provides DNS, HTTPS, DDoS protection, reverse proxying, and identity-aware access control.

Zero Trust means **nothing is trusted by default** - every request is verified. In Cloudflare’s model, services are never directly exposed to the internet via open ports.

A Cloudflare Tunnel creates an **outbound-only encrypted connection** from your server to Cloudflare’s edge.
- No port forwarding
- No public IP required
- Origin server stays private
- Cloudflare handles HTTPS and DNS


## Overview & Setup

### Goal

Expose selected internal services (Homepage, Mealie, Kavita, etc.) securely via example domain:
- https://service.example.com

while keeping the server **fully private**.

**Assumptions**
- Services are already running locally and accessible via LAN
- Docker or native services are working
- You own a domain (purchased via Cloudflare or transferred)

### Architecture

```text
Internet  
↓  
Cloudflare Edge (DNS, HTTPS, Zero Trust)  
↓  
Encrypted Tunnel (outbound)  
↓  
cloudflared daemon on my server  
↓  
Local services (172.16.1.8:PORT)
```


#### Two Routing Approaches (Important)

##### 1.  Cloudflare-Managed Routing (Dashboard)
**How it works**
- You define hostnames → services mapping in the Cloudflare dashboard
- cloudflared service on server runs with a token only
- Local `config.yml` ingress setup is ignored

**Pros**
- Simple
- No cert management
- Easy UI

**Cons**
- Less portable
- Harder to version-control
- Routing logic lives in the cloud


##### 2️. Locally-Defined Routing (What I Did)
**How it works**
- You define all ingress rules in `/etc/cloudflared/config.yml`
- cloudflared service authenticates using a credentials JSON
- DNS points to the tunnel UUID but routing is defined internally

**Pros**
- Fully declarative
- Version-controllable
- Clear, auditable routing
- Easier to reason about

**Cons**
- Slightly more setup


## Step-by-Step Setup

### 1. Assumptions are covered
- You have local services up and running.
- You have domain name ready.

SSH to your server and proceed.

### 2. Install cloudflared (Recommended Method)
We're doing this on modern Ubuntu so best way is to download the `.deb` package from official GitHub repo directly and install it.

```bash
# Go to temp folder
cd /tmp
# Download latest Cloudflared Debian package from GitHub
curl -LO https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
# Install the package
sudo dpkg -i cloudflared-linux-amd64.deb
# Fix dependencies if needed
sudo apt -f install -y
# Verify installation
cloudflared --version
# Optional but not necessary if not already ther, move it to bin to have it system wide
sudo mv /usr/local/bin/cloudflared /usr/bin/cloudflared
```

### 3. Authenticate with Cloudflare

`cloudflared login`

- Opens browser
- Select your domain
- Downloads `cert.pem` to `~/.cloudflared` automatically.

### 4. Create a Tunnel

```bash
cloudflared tunnel create home-tunnel
```

Output:
- Tunnel credentials written to /home/elezthee/.cloudflared/home-tunnel.json
- Tunnel ID: UUID
- Note: this JSON file is required for the service

### 5. Create Tunnel Configuration

```bash
# Create directory for config
sudo mkdir -p /etc/cloudflared 
# Create confing yml file
sudo nano /etc/cloudflared/config.yml
```

Write config to file, make sure to change details according to your setup.

```yaml
# Cloudflare Tunnel configuration
# This file defines which public hostnames map to which internal services
tunnel: <TUNNEL-UUID>
credentials-file: /home/<user>/.cloudflared/home-tunnel.json

ingress:
  - hostname: homepage.example.com
    service: http://172.16.1.8:3000

  - hostname: mealie.example.com
    service: http://172.16.1.8:9925

  - hostname: books.example.com
    service: http://172.16.1.8:5000

  - hostname: audiobooks.example.com
    service: http://172.16.1.8:13378
  
  # Catch-all 404 for unknown requests
  - service: http_status:404
```

Ingress rules are evaluated **top → bottom**.

### 6. Create **CNAME records** in Cloudflare DNS:
In the Cloudflare's dashboard > your domain > DNS > Add records, add an entry for each service.

```text
homepage   → <tunnel UUID>.cfargotunnel.com 
mealie     → <tunnel UUID>.cfargotunnel.com 
books      → <tunnel UUID>.cfargotunnel.com
```

- Proxy must be **ON** (orange cloud)

Alternatively, wildcard approach can be used to minimize DNS administration:

```text
*   → <tunnel UUID>.cfargotunnel.com 
```


### 7. Install clouflared as a Systemd Service

This will create and start the tunnel

```bash
sudo cloudflared --config /etc/cloudflared/config.yml service install 
sudo systemctl enable cloudflared 
sudo systemctl start cloudflared 
sudo systemctl status cloudflared
```

Logs:

```bash
sudo journalctl -u cloudflared -f
```


## Clean-up & Removal

In case you need to reinstall or remove the cloudflared service fully, here's what to do.

```bash
sudo systemctl stop cloudflared       # stop old service
sudo systemctl disable cloudflared    # prevent auto-start
sudo rm -f /etc/systemd/system/cloudflared.service
sudo rm -f /etc/apt/sources.list.d/cloudflare.list
sudo rm -rf /etc/cloudflared
rm -rf ~/.cloudflared
sudo systemctl daemon-reload
ps aux | grep cloudflared             # should show no running process

sudo dpkg -r cloudflared # Uninstall a .deb installation
sudo dpkg -P cloudflared # Remove config files also
```

## Best Practices

- Use **one tunnel per server**
- Keep **catch-all 404** as last ingress rule
- Do NOT expose raw ports publicly
- Use Cloudflare Access later for auth (email, OTP, SSO)
    
- Back up:
    - `/etc/cloudflared/config.yml`
    - `~/.cloudflared/*.json`
        

## Common Pitfalls

- Passing config path as token to `service install`
- Mixing dashboard routing with local ingress
- Wrong library root assumptions (for apps behind tunnel)
- Missing catch-all rule
- Forgetting DNS proxy (orange cloud)