![N8NKALI Banner](https://github.com/ogtamimi/n8n-Kali-Linux/blob/main/banner.png?raw=true)

# N8NKALI — Automated Pentesting & CTF Platform

![Security](https://img.shields.io/badge/Security-Authorized%20Testing-blue) ![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg) ![Docker](https://img.shields.io/badge/Docker-Enabled-blue?logo=docker)

**N8NKALI** combines **Kali Linux** with the **n8n automation platform**, providing a disposable, root-enabled security-testing environment where workflows can execute authorized penetration-testing and CTF tools.

> ⚠️ **For authorized security testing, CTF competitions, and educational use only.**

## Security model

Root execution is intentional because the environment may need to install and execute security packages with elevated privileges. The container is designed to be isolated and disposable.

The assessment endpoint now uses a security gateway that requires an API key, requires the target hostname to be present in `CTF_ALLOWED_HOSTS`, rejects embedded URL credentials, and applies workflow-level request throttling. Security commands are passed through an execution-policy wrapper that blocks shell composition and common host/container escape helpers.

The default Compose configuration binds n8n to `127.0.0.1`. Do not publish the service directly to the public internet without TLS, an authenticated reverse proxy, network isolation, and appropriate operational monitoring.

---

## Quick Start

```bash
git clone https://github.com/ogtamimi/n8n-Kali-Linux.git
cd n8n-Kali-Linux/Docker\ Files
cp .env.example .env
```

Edit `.env` and set real values for:

```text
N8N_ENCRYPTION_KEY=<long-random-secret>
CTF_API_KEY=<long-random-api-key>
CTF_ALLOWED_HOSTS=ctf.local,10.10.20.15
```

Generate strong values with:

```bash
openssl rand -hex 32
```

Then start the environment:

```bash
docker compose up -d --build
```

Open:

```text
http://localhost:5678
```

## Assessment endpoint

The maintained workflow is:

```text
Workflows/ctf-web-assessment-platform.json
```

POST a request containing at least:

```json
{
  "target_url": "http://ctf.local",
  "session_id": "challenge-001",
  "task": "Assess the authorized CTF target and capture the flag"
}
```

Use either:

```text
X-API-Key: <CTF_API_KEY>
```

or:

```text
Authorization: Bearer <CTF_API_KEY>
```

The target hostname must match one of the exact values configured in `CTF_ALLOWED_HOSTS`.

## Command execution policy

The container intentionally runs the workflow engine as root, but security commands are routed through:

```text
/usr/local/bin/n8nkali-exec
```

The wrapper permits a curated set of security and output-processing utilities and rejects shell chaining, redirection, command substitution, interactive shell launchers, and common host/container administration helpers.

This is an application-level control, not a replacement for Docker isolation. Do not mount the Docker socket, host root filesystem, or other privileged host interfaces into the container.

## Installing Additional Tools

Inside the container:

```bash
apt-get update && apt-get install -y <tool-name>
```

Because the environment is root-enabled, additional Kali packages can be installed when needed. Keep installations inside the disposable container and rebuild the image for repeatable deployments.

## Runtime protections

The Compose configuration applies CPU, memory, PID, and n8n production-concurrency limits. Workflow-level request throttling provides an additional backstop against accidental or abusive bursts. For internet-facing deployments, enforce rate limits at a trusted reverse proxy as well.

## Repository Structure

```text
Docker Files/
├── dockerfile
├── docker-compose.yml
├── n8nkali-exec
├── .dockerignore
└── .env.example

Workflows/
├── ctf-web-exploitation-agent.json
├── ctf-web-assessment-platform.json
└── legacy/
    ├── legacy-ctf-agent-v1.json
    └── legacy-ctf-platform-v1.json
```

## Links

- [Docker Hub](https://hub.docker.com/repository/docker/ogtamimi/n8nkali/general)
- [Workflows](https://github.com/ogtamimi/n8n-Kali-Linux/tree/main/Workflows)

## License

Licensed under the **MIT License** — free to use, modify, and distribute with proper credit.

Made with ❤️ by **Omar Al Tamimi**
