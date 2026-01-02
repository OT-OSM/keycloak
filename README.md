# Keycloak Ansible Role

---

## Table of Contents

1. [Overview](#1-overview)
2. [Supported Operating Systems](#2-supported-operating-systems)
3. [Prerequisites & Known Limitations](#3-prerequisites--known-limitations)
4. [Role Structure](#4-role-structure)
5. [Configuration Overview](#5-configuration-overview)
6. [Installation Flow](#6-installation-flow)
7. [Running the Playbook](#7-running-the-playbook)
8. [Validation & Testing](#8-validation--testing)
9. [Best Practices Followed](#9-best-practices-followed)
10. [Troubleshooting](#10-troubleshooting)
11. [Conclusion](#11-conclusion)
12. [References](#12-references)
13. [Author](#13-author)

---

## 1. Overview

Keycloak is an open-source **Identity and Access Management (IAM)** solution used for securing applications and services.

It provides:

* Single Sign-On (SSO)
* User and role management
* Identity federation (OIDC, LDAP, SAML)
* Authentication and authorization
* Support for standalone and clustered (HA) deployments

Official documentation:
[https://www.keycloak.org/documentation](https://www.keycloak.org/documentation)

---

## 2. Supported Operating Systems

| OS Family    | Versions               |
| ------------ | ---------------------- |
| Debian       | Ubuntu 20+, Debian 10+ |
| RedHat       | RHEL 8+, CentOS 8+     |
| Amazon Linux | Amazon Linux 2         |

OS detection is handled using `ansible_os_family`.

---

## 3. Prerequisites & Known Limitations

### System Requirements

| Requirement | Description                 |
| ----------- | --------------------------- |
| RAM         | 4 GB (HA: 6–8 GB preferred) |
| CPU         | 2 vCPU or more              |
| Disk        | 30 GB                       |
| Java        | Java 17+ (Java 21 tested)   |

---

### Python & Ansible Requirements

| Package | Purpose            |
| ------- | ------------------ |
| ansible | Playbook execution |

Install Ansible:

```bash
pip install ansible
```

### Environment Setup (Recommended)

```bash
python3 -m venv ansible-venv
source ansible-venv/bin/activate
pip install ansible
```

---

### Known Platform Limitations

* This role **does not provision databases** (external DB required)
* HA requires **multiple nodes + external DB**
* TLS certificates must be **pre-created**
* Realm import executes **only at startup**

These are **Keycloak platform constraints**, not role limitations.

---

## 4. Role Structure

```
roles/
└── keycloak/
    ├── defaults/main.yml
    ├── vars/main.yml
    ├── handlers/main.yml
    ├── tasks/
    │   ├── main.yml
    │   ├── keycloak.yml
    │   ├── services.yml
    │   └── verify.yml
    ├── templates/
    │   ├── keycloak.conf.j2
    │   ├── keycloak.env.j2
    │   ├── keycloak.service.j2
    │   └── realm-import.json.j2
    └── meta/main.yml

inventory.ini
playbook.yml
group_vars/
```

---

## 5. Configuration Overview

All Keycloak behavior is controlled via variables defined in
`roles/keycloak/defaults/main.yml` and overridden via `group_vars`.

| Configuration Area | Description                              |
| ------------------ | ---------------------------------------- |
| Core Runtime       | Keycloak version, paths, ports, hostname |
| Database           | External PostgreSQL configuration        |
| TLS / HTTPS        | Native HTTPS enablement via certificates |
| Reverse Proxy      | Edge / re-encrypt proxy modes            |
| Identity Providers | OIDC, LDAP, SAML via realm import        |
| High Availability  | Clustering, JGroups, cache stack         |
| Security           | Admin bootstrap and secure env handling  |

---

## 6. Installation Flow

High-level execution flow:

1. Validate OS compatibility
2. Install Java runtime
3. Download and extract Keycloak binary
4. Render `keycloak.conf`
5. Render environment file
6. Build Quarkus optimized runtime
7. Configure systemd service
8. Start and validate Keycloak service
9. Import realm (SSO providers if enabled)

---

## 7. Running the Playbook

```bash
ansible-playbook -i inventory.ini playbook.yml
```

Expected result:

```
failed=0
```

---

## 8. Validation & Testing

### Service Status

```bash
sudo systemctl status keycloak
```

### Port Validation

```bash
ss -lntp | grep 8080
ss -lntp | grep 8443
```

### Browser Access

```
http://<SERVER_IP>:8080
https://<SERVER_IP>:8443
```

---

### HA Validation (Optional)

```bash
cat /opt/keycloak/conf/keycloak.conf | grep -E "cache|cluster|jgroups|node-name"
```

Expected entries:

```
cache=ispn
cache-stack=tcp
cluster-name=keycloak-cluster
```

---

## 9. Best Practices Followed

| Practice               | Description                   |
| ---------------------- | ----------------------------- |
| FQMN                   | ansible.builtin usage         |
| External DB            | Required for prod & HA        |
| TLS via config         | No runtime CLI hacks          |
| Variable-driven        | No hardcoded values           |
| HA validation          | Prevents invalid setups       |
| Idempotency            | Safe re-runs                  |
| Separation of concerns | Install, config, verify split |

---

## 10. Troubleshooting

| Issue                 | Fix                               |
| --------------------- | --------------------------------- |
| Keycloak not starting | `journalctl -u keycloak -n 50`    |
| HTTPS not binding     | Verify cert paths & permissions   |
| HA assertion failure  | Check external DB & JGroups hosts |
| SSO not visible       | Validate realm-import.json        |
| Port conflict         | `ss -lntp`                        |

---

## 11. Conclusion

This role provides a **production-ready, secure, and HA-aware** Keycloak deployment.

It enables teams to:

* Standardize identity infrastructure
* Enable SSO declaratively
* Scale horizontally using HA patterns
* Operate securely with TLS and reverse proxies

---

## 12. References

| Purpose                  | Link                                                                                                               |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------ |
| Keycloak Role Repository | [https://github.com/OT-OSM/keycloak](https://github.com/OT-OSM/keycloak)                                           |
| Keycloak Documentation   | [https://www.keycloak.org/documentation](https://www.keycloak.org/documentation)                                   |
| Keycloak HA Guide        | [https://www.keycloak.org/high-availability/introduction](https://www.keycloak.org/high-availability/introduction) |
| Ansible Documentation    | [https://docs.ansible.com](https://docs.ansible.com)                                                               |

---

## 13. Author

**Author:** Divya Mishra

**Last Updated:** 29-Dec-2025

---
