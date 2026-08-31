![N8NKALI Banner](https://github.com/ogtamimi/n8n-Kali-Linux/blob/main/banner.png?raw=true)

# N8NKALI — Automated Pentesting & CTF Platform

![Security](https://img.shields.io/badge/Security-Authorized%20Testing-blue) ![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg) ![Docker](https://img.shields.io/badge/Docker-Enabled-blue?logo=docker)

**N8NKALI** combines **Kali Linux** with the **n8n automation platform**, providing a disposable, root-enabled security-testing environment where n8n workflows can execute and install authorized penetration-testing and CTF tools.

> ⚠️ **For authorized security testing, CTF competitions, and educational use only.**

## Why root?

Root execution is intentional. Security workflows may need to install packages with `apt` and run tools that require elevated privileges. The recommended deployment keeps N8NKALI isolated in Docker and binds the UI to localhost by default.

**Do not expose an unauthenticated N8NKALI instance directly to the public internet.** If you expose webhooks externally, add authentication, target authorization/scope validation, rate limiting, and execution limits.

---

## Quick Start

```bash
git clone https://github.com/ogtamimi/n8n-Kali-Linux.git
cd n8n-Kali-Linux/Docker\ Files
cp .env.example .env
# Edit .env and set N8N_ENCRYPTION_KEY to a long random value
docker compose up -d --build
```

Then open:

```text
http://localhost:5678
```

The Compose configuration binds port 5678 to `127.0.0.1` by default so the n8n UI is not exposed on every network interface.

## Accessing the Terminal

```bash
docker exec -it n8n-kali bash
```

The shell is root by design. The container should be treated as a disposable security-testing environment.

## Running Tools via n8n Workflows

Use n8n's **Execute Command** node to run authorized security tools.

### Gobuster

```bash
gobuster dir -u http://target.example -w /usr/share/seclists/Discovery/Web-Content/common.txt
```

### SQLMap

```bash
sqlmap -u "http://target.example/page?id=1" --batch --level=3
```

### Nikto

```bash
nikto -h http://target.example
```

### WhatWeb

```bash
whatweb http://target.example
```

### httpx

```bash
echo "target.example" | httpx -status-code -title -tech-detect
```

Only run these against systems you own or have explicit permission to test.

---

## Installing Additional Tools

Inside the container:

```bash
apt-get update && apt-get install -y <tool-name>
```

Or from an n8n Execute Command node:

```bash
apt-get update && apt-get install -y ffuf
```

Because the environment is root-enabled, workflows can install additional Kali packages when required.

---

## Docker Notes

- `N8N_VERSION` can be set to a specific release in `.env` for reproducible builds.
- The container intentionally runs as root.
- Persistent n8n data is stored in `./n8n_data`.
- The default Compose configuration binds n8n to localhost only.
- Never commit `.env` or real encryption keys.
- For internet-facing deployments, put n8n behind TLS and authentication and implement webhook/target authorization.

## Repository Structure

```text
Docker Files/
├── dockerfile
├── docker-compose.yml
├── .dockerignore
└── .env.example

Workflows/
├── High end Workflow.json
└── High end Project.json
```

## Links

- [Docker Hub](https://hub.docker.com/repository/docker/ogtamimi/n8nkali/general)
- [Workflows](https://github.com/ogtamimi/n8n-Kali-Linux/tree/main/Workflows)

## License

Licensed under the **MIT License** — free to use, modify, and distribute with proper credit.

Made with ❤️ by **Omar Al Tamimi**
