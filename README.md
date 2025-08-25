[![Releases](https://img.shields.io/github/v/release/woqzxje/tailscale-nextcloud-docker?label=Releases&color=2b9348)](https://github.com/woqzxje/tailscale-nextcloud-docker/releases)

# Self-hosted Nextcloud on macOS with Tailscale, Caddy, Docker

![Nextcloud + Tailscale + Docker](https://user-images.githubusercontent.com/4354155/120042488-7a5f7280-bfdd-11eb-9b0f-9f4b0b42b4c8.png)

A reproducible setup to run Nextcloud on macOS using Docker (Colima). Tailscale secures remote access. Caddy provides automatic HTTPS using Let's Encrypt and MagicDNS. Grafana and Prometheus collect and show system and container metrics. You keep full control of your data and access.

Topics: caddy · colima · docker · grafana · macos · magic-dn · monitoring · nextcloud · personal-cloud · private-clo · prometheus-exporter · reverse-proxy · self-hosted · tailscale · vpn

---

Contents

- Features
- Architecture diagram
- Requirements
- Quick start
- Manual install (compose files)
- Tailscale setup
- Caddy configuration
- Monitoring (Prometheus + Grafana)
- Backups
- Security notes
- Troubleshooting
- Contributing
- Releases

---

Features 🚀

- Run Nextcloud on macOS with Docker (Colima).
- Secure remote access via Tailscale VPN and MagicDNS.
- Automatic HTTPS with Caddy reverse proxy.
- Metrics: Prometheus exporters + Grafana dashboards.
- Simple docker-compose setup. Persistent storage.
- Optional system service scripts for macOS users.
- Minimal external dependencies. You control the stack.

Architecture 🏗️

![Architecture diagram](https://raw.githubusercontent.com/woqzxje/tailscale-nextcloud-docker/main/docs/arch-diagram.png)

- macOS host runs Colima (container runtime for macOS).
- Docker Compose runs:
  - nextcloud (PHP + nginx)
  - mariaDB or Postgres
  - caddy (reverse proxy, TLS)
  - tailscale client (accepts exit node or connects to network)
  - prometheus + node_exporter + cAdvisor
  - grafana with prebuilt dashboard
- Tailscale provides private DNS (MagicDNS). You reach your Nextcloud over a stable, private name and secure tunnel.

Requirements ✅

- macOS 12+ (Intel or Apple Silicon)
- Homebrew (recommended)
- Colima (for Docker runtime on macOS)
- Docker CLI
- Git
- Tailscale account (free tier works)
- At least 4 GB RAM free for containers
- Disk space for data (suggest 20+ GB for typical use)

Quick start — automated release script

Download the installer from the Releases page and run it. The release contains the install script and packaged assets. Download the release asset (for example, install.sh) from:

https://github.com/woqzxje/tailscale-nextcloud-docker/releases

Example command (replace vX.Y.Z with the actual release tag or use the latest link shown on Releases):

```bash
# Download the release asset (example)
curl -L -o install.sh "https://github.com/woqzxje/tailscale-nextcloud-docker/releases/download/vX.Y.Z/install.sh"
chmod +x install.sh
./install.sh
```

The script sets up Colima, pulls the docker-compose stack, and prompts for Tailscale auth. If the link does not work, check the Releases section of this repository for the correct asset to download and run.

Manual install — step by step

1. Install Homebrew, Docker CLI, and Colima

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install docker colima
colima start --cpu 2 --memory 4
```

2. Clone this repo

```bash
git clone https://github.com/woqzxje/tailscale-nextcloud-docker.git
cd tailscale-nextcloud-docker
```

3. Copy the example env and configure

```bash
cp .env.example .env
# edit .env: NEXTCLOUD_DOMAIN, DB_PASSWORD, TAILSCALE_AUTHKEY or follow Tailscale auth flow
```

4. Start the stack

```bash
docker compose up -d
```

5. Follow Nextcloud web setup at your MagicDNS name or local host

Tailscale setup 🔒

- Install Tailscale on your macOS host or run the tailscale client inside a container. For host install:

```bash
brew install --cask tailscale
sudo tailscale up
```

- Enable MagicDNS and set an auth key or complete the OAuth flow at https://login.tailscale.com/admin

- Option: run tailscale in a container and register an auth key. Use the official Tailscale image or the embedded client in the repo compose file.

Caddy and HTTPS certificates 🔐

- Caddy handles TLS automatically.
- Configure NEXTCLOUD_DOMAIN in .env to a hostname you control or your Tailscale MagicDNS name (example: my-nextcloud.tailnet-yourname.ts.net).
- Caddy uses ACME to request certificates. When you use MagicDNS and Tailscale, Caddy can obtain valid certs if the domain resolves publicly or via ACME DNS challenge. For private-only domains, Caddy will still provide local valid certs via local CA or use self-signed certs. The included config prefers ACME and falls back to internal certs.

Example Caddy site snippet (from docker-compose config):

```caddyfile
https://{env.NEXTCLOUD_DOMAIN} {
    reverse_proxy nextcloud:80
    tls {
        # ACME settings can be set via environment variables
    }
    header {
        Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
        X-Content-Type-Options "nosniff"
    }
}
```

Storage and volumes 💾

- Data and DB volumes mount to the host under ./data and ./db by default.
- Use external drives or APFS volumes for large storage.
- Keep backups of both Nextcloud data and the database.

Monitoring — Prometheus, cAdvisor, Grafana 📊

- Prometheus scrapes exporters:
  - node_exporter (host metrics)
  - cadvisor (container metrics)
  - mysql_exporter / postgres_exporter for DB stats
- Grafana includes dashboards for system and Nextcloud metrics.
- Access Grafana at http://localhost:3000 or via your reverse proxy if you expose it.

Prometheus snippet (docker-compose):

```yaml
prometheus:
  image: prom/prometheus:latest
  volumes:
    - ./monitoring/prometheus.yml:/etc/prometheus/prometheus.yml:ro
```

Grafana provisioning includes a default dashboard for Nextcloud. You can import community dashboards for Nextcloud, MySQL/MariaDB, and Docker.

Backups and restore 🔁

- Backup data folder (./data) and DB dump.
- Example DB dump:

```bash
docker compose exec db mysqldump -u root -p"${DB_ROOT_PASSWORD}" nextcloud > nextcloud.sql
tar -czf nextcloud-data-$(date +%F).tar.gz ./data
```

- Restore by stopping containers, restoring files and DB, then starting the stack.

Security checklist 🔒

- Use Tailscale for remote admin access; avoid exposing ports to the public internet.
- Set strong DB and Nextcloud admin passwords.
- Lock down Caddy and restrict possible host headers.
- Enable two-factor auth in Nextcloud for user accounts.
- Regularly update containers and images.

Troubleshooting 🛠️

- Caddy fails to get a cert:
  - Check domain resolution. Use the Tailscale MagicDNS name or a public domain with correct DNS.
  - Review Caddy logs: docker compose logs caddy
- Nextcloud shows file permission errors:
  - Ensure data volume uid/gid matches web server user.
  - Use chown -R 33:33 ./data if using official Nextcloud images (www-data UID 33).
- Tailscale auth issues:
  - Run tailscale up on the host and confirm it appears on the admin console.
  - Use an auth key if you run Tailscale in a container.

FAQ ❓

Q: Why Colima instead of Docker Desktop?
A: Colima uses Lima and offers a lighter-weight VM. It runs on both Intel and Apple Silicon.

Q: Can I use an external database?
A: Yes. Point the compose env variables to your DB host and credentials.

Q: How do I access Nextcloud from outside my network?
A: Use Tailscale MagicDNS or expose Caddy with a public domain and valid DNS pointing to your router (less secure).

Contributing 🤝

- Open an issue for feature requests or bugs.
- Send pull requests for improvements.
- Follow the code style in the repo and add tests where applicable.

Releases & installer

Download and run the release asset (installer) from the Releases page:

https://github.com/woqzxje/tailscale-nextcloud-docker/releases

If the release contains a packaged installer, download that file and execute it. The badge at the top links to the same Releases page.

Useful links and images

- Nextcloud: https://nextcloud.com/ ![Nextcloud](https://upload.wikimedia.org/wikipedia/commons/3/32/Nextcloud_Logo.png)
- Tailscale: https://tailscale.com/ ![Tailscale](https://tailscale.com/static/images/brand/tailscale-mark-blue.svg)
- Caddy: https://caddyserver.com/ ![Caddy](https://caddyserver.com/resources/caddy-icon-256.png)
- Colima: https://github.com/abiosoft/colima
- Grafana: https://grafana.com/
- Prometheus: https://prometheus.io/

License

This repository uses the MIT License. See the LICENSE file for details.