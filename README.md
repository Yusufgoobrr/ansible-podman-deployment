# Ansible Podman Deployment

> Automated container deployment and management using Ansible and Podman on Linux.

![Ansible](https://img.shields.io/badge/Ansible-2.15+-red)
![Podman](https://img.shields.io/badge/Podman-4.0+-purple)
![License](https://img.shields.io/badge/license-MIT-green)

This project automates the deployment and management of a Podman container on a Linux target host using Ansible. It pulls a Docker image from a public registry, configures port and volume mappings, injects environment variables securely via Ansible Vault, and verifies the container is running — all without manual intervention.

---

## 📑 Table of Contents

- [Features](#-features)
- [Project Structure](#️-project-structure)
- [Prerequisites](#-prerequisites)
- [Configuration](#-configuration)
- [Secrets Management](#-secrets-management)
- [Usage](#-usage)

---

## ✨ Features

- **Fully automated deployment.** One command pulls the image, creates directories, starts the container, and confirms it is running.
- **Idempotent.** Safe to run multiple times — Ansible only makes changes when needed.
- **Parameterized.** Image name, tag, container name, ports, and volumes are all driven by variables.
- **Secure secrets.** Sensitive values like API tokens are encrypted with Ansible Vault and never stored in plain text.
- **Cross-distro.** Uses the generic `package` module so it works on Debian, Ubuntu, RHEL, and Fedora.
- **Container verification.** Automatically checks and prints the container status after deployment.

---

## 🗂️ Project Structure

```
ansible-podman-deployment/
├── inventory.ini          # Target host(s) and connection details
├── playbook.yml           # Main Ansible playbook
├── requirements.yml       # Ansible collection dependencies
├── group_vars/
│   ├── all.yml            # Default variables
│   └── vault.yml          # Encrypted secrets (Ansible Vault)
└── README.md
```

---

## 🚀 Prerequisites

### Control machine (where you run Ansible)

| Tool | Version |
|------|---------|
| Python | 3.8+ |
| Ansible | 2.15+ |

Install Ansible and the required collection:

```bash
pip install ansible
ansible-galaxy collection install -r requirements.yml
```

### Target host

- Linux (Debian / Ubuntu / RHEL / Fedora)
- SSH access configured
- Python 3 available
- Sudo privileges for the Ansible user

---

## ⚙️ Configuration

All variables are defined in `group_vars/all.yml`:

| Variable | Description | Example |
|----------|-------------|---------|
| `image_name` | Full image name including registry | `docker.io/yusufgoobrr/pulsedesk` |
| `image_tag` | Image tag | `latest` |
| `container_name` | Name for the running container | `my-app` |
| `ports` | List of port mappings (host:container) | `["8080:8080"]` |
| `volumes` | List of volume mappings (host:container) | `["/opt/my-app/data:/app/data"]` |
| `hf_token` | HuggingFace API token (via Vault) | `{{ vault_hf_token }}` |

Update `inventory.ini` to point to your target host:

```ini
[servers]
app-server ansible_host=YOUR_HOST_IP ansible_user=YOUR_USER
```

---

## 🔒 Secrets Management

Sensitive values are encrypted using Ansible Vault. To create the vault file:

```bash
ansible-vault create group_vars/vault.yml
```

Add the following inside:

```yaml
vault_hf_token: "your_actual_token_here"
```

To edit the vault later:

```bash
ansible-vault edit group_vars/vault.yml
```

The encrypted `vault.yml` is safe to commit to version control. Never commit plain text secrets.

---

## 🌍 Usage

**Run the playbook:**

```bash
ansible-playbook -i inventory.ini playbook.yml --ask-pass --ask-become-pass --ask-vault-pass
```

**What the playbook does:**

1. Installs Podman on the target host
2. Creates volume directories on the host
3. Pulls the container image from the registry
4. Runs the container with the configured ports, volumes, and environment variables
5. Verifies the container is running and prints its status

**Verify manually on the target host:**

```bash
sudo podman ps
curl http://localhost:8080
```