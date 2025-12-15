# NGINX Monitoring Reverse Proxy Setup (Ansible)

This project provides an Ansible playbook for setting up an NGINX server as a reverse proxy for monitoring tools like Grafana, Prometheus, and Alertmanager. It includes optional basic authentication and TLS/SSL configuration support.

---

## 📚 Table of Contents

- [Introduction](#introduction)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Dependencies](#dependencies)
- [Examples](#examples)
- [Troubleshooting](#troubleshooting)
- [Contributors](#contributors)
- [License](#license)

---

## 📖 Introduction

This Ansible configuration automates the provisioning of an NGINX server, sets up basic HTTP authentication (optional), and configures reverse proxy settings for various monitoring endpoints. It's designed to be secure by default, supporting TLS and service restart triggers on configuration changes.

---

## ✨ Features

- Installs and configures the NGINX server
- Sets up reverse proxies for:
  - Grafana
  - Prometheus
  - Alertmanager
- Supports basic HTTP authentication (optional)
- TLS/SSL certificate configuration per service
- Modular and customizable via Ansible variables
- Automatically restarts NGINX when configuration changes

---

## ⚙️ Installation

### Prerequisites

- Ansible installed (`ansible>=2.9`)
- Root or sudo privileges on target machines

### Steps

1. Clone the repository containing these playbooks.
2. Configure the necessary variables in your inventory or playbook.
3. Run the playbook:

```bash
ansible-playbook -i inventory.ini main.yaml
```

---

## 🚀 Usage

The main playbook performs the following tasks:

- Installs the `nginx` package
- Optionally sets up basic authentication using credentials from `vault_nginx_basic_auth_http_value`
- Deploys reverse proxy configurations using a Jinja2 template: `nginx-monitoring.j2`
- Enables TLS with individual certificates per service
- Restarts NGINX when configuration is updated

---

## 🛠 Configuration

Key Ansible variables used:

| Variable                         | Description                                         | Example Value                                |
|----------------------------------|-----------------------------------------------------|----------------------------------------------|
| `nginx_basic_auth_file`          | Path to the `.htpasswd` file                        | `/etc/nginx/.htpasswd`                        |
| `nginx_basic_auth_http_value`    | HTTP Basic Auth value (from vault)                  | `{{ vault_nginx_basic_auth_http_value }}`    |
| `nginx_tls_dir`                  | Directory holding TLS certificates                  | `/etc/nginx/tls`                              |
| `nginx_monitoring_sites`         | List of monitoring endpoints                        | See [Examples](#examples) section below      |
| `nginx_monitoring_sites_domain`  | Main domain used for all sites                      | `fogc.space`                                  |
| `nginx_monitoring_basic_auth`    | Enable/disable basic auth per service               | `false` or `true`                             |

---

## 📦 Dependencies

- NGINX
- Ansible built-in modules:
  - `ansible.builtin.copy`
  - `ansible.builtin.template`
  - `ansible.builtin.systemd`

---

## 🧪 Examples

Here are sample entries for `nginx_monitoring_sites`:

```yaml
nginx_monitoring_sites:
  - server_name: 'grafana.fogc.space'
    ssl_certificate: '/etc/nginx/tls/grafana.pem'
    ssl_certificate_key: '/etc/nginx/tls/grafana.key'
    backend: 'http://localhost:3000/'
    config_name: monitoring-grafana

  - server_name: 'prometheus.fogc.space'
    ssl_certificate: '/etc/nginx/tls/prometheus.pem'
    ssl_certificate_key: '/etc/nginx/tls/prometheus.key'
    backend: 'http://localhost:9090/'
    basic_auth: false
    config_name: monitoring-prometheus
```

---

## 🐞 Troubleshooting

- **NGINX fails to start**: Check the syntax of the rendered config files in `/etc/nginx/sites-enabled/`.
- **Basic Auth not working**: Ensure `vault_nginx_basic_auth_http_value` is properly set.
- **TLS errors**: Confirm certificates exist at the specified `ssl_certificate` and `ssl_certificate_key` paths.

---

## 👥 Contributors

- Maintained by: *Andris Zbitkovskis*

---

## 📄 License


```
MIT License
```