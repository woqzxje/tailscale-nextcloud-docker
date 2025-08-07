# Self-Hosted Nextcloud with Docker, Caddy, and Tailscale

This project lets me run my own private Nextcloud server on my Mac using Docker Compose, with secure remote access through Tailscale’s MagicDNS and HTTPS provided by Caddy as a reverse proxy. It’s private, secure, and accessible only within my Tailscale network.

---

## 🔧 Why I Built This

I wanted a personal cloud service—like Google Drive but hosted by me—so I could keep my files private without relying on third-party servers. It was also a way to learn Docker networking, VPNs with Tailscale, and secure web access with Caddy.

---

## ⚙️ Technologies Used

- **Nextcloud** for cloud storage and syncing  
- **Docker Compose** to manage containers  
- **MariaDB** as the Nextcloud database  
- **Redis** to improve performance  
- **Caddy** for automatic HTTPS and reverse proxy  
- **Tailscale** for a private VPN and MagicDNS  
- **Colima** to run Docker on macOS with Linux networking  

---

## 🧱 Folder Structure

nextcloud-docker-setup/
├── docker-compose.yml
├── Caddyfile
├── caddy_config/ # Caddy runtime config (auto-generated)
├── caddy_data/ # Caddy certs & TLS data (auto-generated)
├── screenshots/ # Screenshots folder
│ ├── architecture-diagram.png
│ ├── nextcloud-ui.png
│ └── tailscale-dashboard.png
├── README.md
└── .gitignore

---

![architecture](/screenshots/Architecture.png)
![account](/screenshots/nextcloud%20account%20creation.png)
![recommendation](/screenshots/apps%20recommend.png)
![dashboard](/screenshots/dashboard.png)
![filemanager](/screenshots/file%20manager.png)
---

## 🚧 Challenges

- Needed Colima because Docker can’t easily access the macOS network  
- Caddy reverse proxy required correct port and hostname setup  
- MagicDNS short names didn’t resolve until fixing macOS DNS  
- Learned that SSL setup is still useful even when using Tailscale VPN  

---

## 📚 What I Learned

- Managing Docker containers with shared volumes and networks  
- Using Colima for Linux-style Docker on macOS  
- Securing local services with Caddy reverse proxy and HTTPS  
- How Tailscale MagicDNS simplifies device-to-device access  
- Troubleshooting DNS and networking on different platforms  

---

## ✅ Future Plans

- Add backups for Nextcloud data  
- Configure email for password recovery  
- Install useful Nextcloud plugins  
- Enable two-factor authentication for admins  

---

## 🏁 How to Run

1. Clone the repo:

```shell
    git clone https://github.com/100dollarguy/tailscale-nextcloud-docker.git
    cd tailscale-nextcloud-docker
```    

2. Start Colima and the Docker stack:

```shell
    colima start
    docker compose up -d
```

3. Access Nextcloud in your browser via Tailscale domain:

```shell
    http://rohans-macbook-air.wolf-humboldt.ts.net   (Your Tailscale IP or Magic DNS)
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