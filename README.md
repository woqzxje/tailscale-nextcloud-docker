# 🗂️ Self-Hosted Nextcloud with Docker, Caddy, Prometheus, Grafana & Tailscale

This project lets me run a fully self-hosted, private cloud storage and monitoring stack on macOS using **Docker Compose**, with secure remote access through **Tailscale’s MagicDNS**, **HTTPS** via **Caddy**, and real-time monitoring using **Prometheus + Grafana**. It’s secure, private, and entirely within my control.

---

## 🔧 Why I Built This

I wanted a **private Google Drive alternative** where I controlled my files completely. I also wanted to learn:

- Docker containerization
    
- VPN-based secure access using Tailscale
    
- Configuring HTTPS with reverse proxies (Caddy)
    
- Observability using Grafana and Prometheus
    

---

## ⚙️ Technologies Used

- **Nextcloud** – cloud file syncing and collaboration
    
- **MariaDB** – database backend
    
- **Redis** – caching for Nextcloud performance
    
- **Caddy** – reverse proxy with automatic HTTPS
    
- **Prometheus** – metrics collection
    
- **Grafana** – visual dashboards for system health
    
- **Tailscale** – private VPN with MagicDNS
    
- **Colima** – Docker on macOS with Linux networking
    

---

## 🧱 Architecture

![architecture](/screenshots/Architecture.png)

---

## 🔐 Tailscale Access

![tailscale](/screenshots/taiscale.png)

---

## 🧑‍💻 Account Creation Page

![account](/screenshots/nextcloud%20account%20creation.png)

---

## ⭐ Recommended Nextcloud Apps

![recommendation](/screenshots/apps%20recommend.png)

---

## 📁 Nextcloud Dashboard

![dashboard](/screenshots/dashboard.png)

---

## 📂 File Manager

![filemanager](/screenshots/file%20manager.png)

---

## 📊 Grafana login

_Configure Grafana to monitor your container metrics and Nextcloud performance._

![Grafana login](/screenshots/Grafana%20login.png)

## Grafana Dashboard

![Grafana](/screenshots/Grafana%20Dashboard.png)

---

## 🧪 Prometheus Metrics

_Set up Prometheus to scrape Docker and system metrics for monitoring._


![prometheus](/screenshots/Prometheus.png)

![prometheus](/screenshots/Prometheus%20metrics.png)

---

## 🧯 Troubleshooting: Blank Login Page on Nextcloud

When I first set up Nextcloud, the login page was **completely blank**. Here's what fixed it:

### ✅ Root Causes

- Database not running or misconfigured
    
- Incorrect `config.php` settings
    
- File permission issues
    
- Services not restarted after changes
    
- Cached browser errors
    

### 🛠️ Fixes

1. **Checked MariaDB service**
    
2. **Verified `config/config.php` database fields**
    
3. **Ensured correct file permissions for web user**
    
4. **Restarted containers**
    
5. **Cleared browser cache and cookies**
    

After doing all of the above, the login page loaded correctly.

---

## 🧯 Troubleshooting: Grafana Blank Page or Not Loading

When Grafana didn’t load its login/dashboard:

### ✅ Common Problems

- Grafana not connected to its DB (SQLite or external)
    
- Misconfigured `grafana.ini` (root URL or port)
    
- Reverse proxy or firewall blocking access
    

### 🛠️ Fixes

1. Checked `docker logs` and confirmed Grafana is running
    
2. Verified `grafana.ini` config (port, URLs)
    
3. Cleared browser cache
    
4. Restarted Grafana container
    

---

## 🧯 Troubleshooting: Prometheus Not Scraping or Showing Metrics

Prometheus showed empty dashboards because it wasn’t scraping correctly.

### ✅ Common Problems

- Misconfigured `prometheus.yml`
    
- Scrape targets unreachable
    
- Network or firewall blocking port 9090
    

### 🛠️ Fixes

1. Checked Prometheus container logs
    
2. Validated scrape target URLs in `prometheus.yml`
    
3. Restarted Prometheus after edits
    
4. Cleared cache in browser
    

---

## 📚 What I Learned

- Docker Compose for multi-service orchestration
    
- Linux-based networking with Colima
    
- Reverse proxies and automatic TLS with Caddy
    
- VPN routing and access control using Tailscale
    
- Real-time monitoring using Prometheus and Grafana
    
- Debugging blank pages, database issues, config problems
    

---

## ✅ Future Plans

- Automated **Nextcloud backups**
    
- Email configuration for password resets
    
- Add **Grafana alerts** for system health
    
- Enable **2FA** in Nextcloud for better security
    
- Explore **external storage backends** for Nextcloud
    

---

## 🏁 How to Run

1. Clone the repo:
    

```shell

git clone https://github.com/100dollarguy/tailscale-nextcloud-docker.git

 cd tailscale-nextcloud-docker

```
2. Start Colima (Docker for macOS):
    
```shell

colima start

```
3. Start all containers:
    

```shell

docker compose up -d

```
4. Access services:
    
```shell
- 📂 Nextcloud:  
    http://your-magicdns-name.ts.net
    
- 📊 Grafana:  
    http://your-magicdns-name.ts.net:3000
    
- 🔍 Prometheus:  
    http://your-magicdns-name.ts.net:9090
```
---

## 🔒 Important

This setup is intended for private use within a Tailscale VPN. Do **not** expose it publicly without adding extra security measures.

---

If you have any questions or want help extending or customizing this setup, feel free to open issues or reach out!

---

*Happy self-hosting!*


## ✨ License

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

This project is licensed under the [MIT License](LICENSE).
You're free to use, modify, and share it — personally or commercially.

Feel free to fork it, improve it for your own setup, or share with others!