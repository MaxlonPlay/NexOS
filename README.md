# <img src="logo.png" alt="NexOS" width="32" style="vertical-align:middle"> NexOS

A web-based management interface for Linux systems with ZFS storage. NexOS lets you administer your server entirely from a browser — no command line required.

![Dashboard](dashboard.png)

---

## What it does

NexOS provides a unified control panel for the tasks that normally require juggling multiple CLI tools:

- **ZFS storage** — create and manage pools, datasets, volumes, and snapshots; run scrubs, TRIM, resilver, and replace operations
- **Disk management** — partition, format, mount, and unmount physical disks and USB drives; view SMART data and temperature history
- **File manager** — browse, upload, download, copy, move, rename, and archive files directly in the browser; built-in text editor
- **Restic backups** — schedule encrypted backups to local or remote repositories with pre/post script support; browse and restore snapshots
- **Network shares** — configure SMB and NFS exports; manage Samba users, groups, and global settings
- **Network mounts** — mount remote SMB/NFS resources and persist them across reboots
- **Download manager** — queue HTTP, magnet, and torrent downloads via aria2; manage active transfers in real time
- **Scheduler** — automate ZFS operations and dataset tasks with a cron-based scheduler
- **System monitoring** — live I/O, CPU, RAM, network, and ARC statistics via real-time streams
- **Console** — full interactive terminal session in the browser
- **Notifications** — event alerts delivered via Telegram or email; configurable per event type
- **Settings backup** — export and restore your entire NexOS configuration to an encrypted `.nexbak` file
- **Updates** — check for and install new releases directly from the interface

---

## Who it is for

NexOS is designed for anyone running a Linux server with ZFS who wants a clean web interface instead of a terminal. It works equally well on a home NAS, a Proxmox host, or a dedicated server.

---

## Security

NexOS is built with security as a first-class requirement.

- **Authentication** — bcrypt password hashing with timing-attack-resistant verification; session tokens with short-lived JWT access tokens and automatic sliding refresh
- **Two-factor authentication** — TOTP (compatible with any authenticator app) with QR code setup, backup codes, and brute-force lockout
- **CSRF protection** — every mutating endpoint requires a one-time ticket in addition to a valid session; tickets are consumed on first use
- **Encryption at rest** — sensitive database fields (credentials, API tokens, TOTP secrets) are encrypted with a Fernet key stored separately from the database
- **TLS** — native HTTPS with self-signed or custom certificates, or reverse-proxy mode for deployments behind nginx/Caddy/Traefik
- **HTTP security headers** — CSP, HSTS, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy
- **Host validation** — configurable allowed origins; requests from unlisted hosts are rejected before any processing
- **WebSocket security** — short-lived ticket authentication (5-second TTL) with periodic session revalidation every 60 seconds; single session per user
- **Package integrity** — the installer verifies every file in the release package against Ed25519 signatures before writing anything to disk; tampered or incomplete packages are rejected

---

## Requirements

- Linux with **systemd**
- **ZFS** kernel modules (OpenZFS)
- **root** access (required for ZFS and disk operations)
- A modern browser

Optional:
- `aria2` for the download manager (`apt install aria2` / `dnf install aria2`)
- `restic` for backups (the interface can install it automatically)

---

## Installation

**1. Download the latest release**

Go to the [Releases](../../releases/latest) page and download `nexos.tar.gz`.

**2. Extract**

```bash
tar -xzf nexos.tar.gz
cd nexos
```

**3. Run the installer**

```bash
sudo python3 installer.py
```

The installer will guide you through a short wizard:

| Step | What it asks |
|------|-------------|
| Connection mode | Native HTTPS · HTTPS via reverse proxy · Plain HTTP |
| Certificate | Auto-generate self-signed or provide your own cert/key |
| Port | Listening port (default: `8000`) |
| File manager root | Which path to expose (default: `/`) and protection mode |
| Allowed origins | Restrict access to specific hostnames or IPs (optional) |

When finished, NexOS is installed as a systemd service and starts automatically.

**4. Open the interface**

Navigate to the address shown at the end of the installer (e.g. `https://your-server:8000`).  
On first login you will be prompted to create your admin credentials.

---

## Managing the service

```bash
systemctl status nexos      # current status and memory usage
systemctl restart nexos     # restart
systemctl stop nexos        # stop
journalctl -u nexos -f      # live logs
```

---

## Reinstalling or reconfiguring

Running the installer again on an existing installation will walk through the same wizard while preserving your current values — press Enter to keep any setting unchanged. Your database and encryption key are never touched during a reinstall.

---

## License

[AGPL-3.0-or-later](LICENSE)
