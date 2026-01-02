# **Keycloak Ansible Role**

---

## **Author Metadata**

| Created by   | Created on | Version | Last Updated On |
| ------------ | ---------- | ------- | --------------- |
| Divya Mishra | 14-12-2025 | V1      | 14-12-2025      |

---

## **Table of Contents**

1. [Introduction](#1-introduction)
2. [Objectives](#2-objectives)
3. [Supported Platforms](#3-supported-platforms)
4. [Prerequisites](#4-prerequisites)
5. [Directory Structure](#5-directory-structure)
6. [Implementation Details](#6-implementation-details)

   * [6.1 OS & Java Compatibility Validation](#61-os--java-compatibility-validation)
   * [6.2 Parameterized Keycloak Installation](#62-parameterized-keycloak-installation)
   * [6.3 Database Configuration (PostgreSQL)](#63-database-configuration-postgresql)
   * [6.4 TLS / HTTPS & Reverse Proxy Readiness](#64-tls--https--reverse-proxy-readiness)
7. [Execution Using Ansible](#7-execution-using-ansible)
8. [Validation & Health Checks](#8-validation--health-checks)
9. [Troubleshooting](#9-troubleshooting)
10. [Best Practices Implemented](#10-best-practices-implemented)
11. [Conclusion](#11-conclusion)
12. [References](#12-references)

---

## **1. Introduction**

Keycloak is an open-source **Identity and Access Management (IAM)** solution providing authentication, authorization, and identity federation.

This Ansible role automates a **modern, production-aligned Keycloak deployment**, focusing on:

* Latest Keycloak (Quarkus-based) releases
* Updated OS and Java compatibility
* Secure, systemd-managed execution
* TLS / HTTPS and reverse-proxy readiness

Official documentation: [https://www.keycloak.org/documentation](https://www.keycloak.org/documentation)

---

## **2. Objectives**

* Validate support for **latest Keycloak versions**
* Remove dependency on **legacy OS and Java runtimes**
* Parameterize Keycloak version and archive URL
* Enforce **Java 17+**
* Enable **TLS / HTTPS and reverse proxy support as options**
* Ensure **production mode execution** (`kc.sh start`)

---

## **3. Supported Platforms**

### Operating Systems

| OS Family       | Versions            |
| --------------- | ------------------- |
| Debian / Ubuntu | 20.04, 22.04, 24.04 |

> Legacy platforms (Ubuntu 16/18, CentOS 6/7) are **intentionally not supported**.

### Java Runtime

| Runtime | Version |
| ------- | ------- |
| OpenJDK | 17+     |

---

## **4. Prerequisites**

### System Requirements

| Component | Requirement          |
| --------- | -------------------- |
| OS        | Ubuntu 20.04 / 22.04 |
| Java      | OpenJDK 17+          |
| RAM       | 4 GB minimum         |
| CPU       | 2 vCPU               |
| Disk      | 5 GB free            |
| Access    | SSH + sudo           |

---

## **5. Directory Structure**

```
roles/
└── keycloak/
    ├── defaults/main.yml
    ├── handlers/main.yml
    ├── tasks/
    │   ├── prerequisites.yml
    │   ├── postgresql.yml
    │   ├── keycloak.yml
    │   ├── services.yml
    │   └── verify.yml
    ├── templates/
    │   ├── keycloak.conf.j2
    │   └── keycloak.service.j2
    └── vars/main.yml
```

---

## **6. Implementation Details**

### **6.1 OS & Java Compatibility Validation**

```yaml
assert:
  - ansible_distribution == "Ubuntu"
  - ansible_distribution_major_version | int >= 20
  - java_version | int >= 17
```

---

### **6.2 Parameterized Keycloak Installation**

```yaml
keycloak_version: "26.0.7"
keycloak_archive_url: "https://github.com/keycloak/keycloak/releases/download/{{ keycloak_version }}/keycloak-{{ keycloak_version }}.tar.gz"
keycloak_install_dir: "/opt/keycloak"
```

Installation layout:

```
/opt/keycloak-26.0.7
/opt/keycloak → symlink
```

---

### **6.3 Database Configuration (PostgreSQL)**

```yaml
postgres_host: "localhost"
postgres_port: 5432
keycloak_db_name: "keycloak"
keycloak_db_user: "keycloak"
keycloak_db_password: "StrongPassword"
```

Verification:

```bash
sudo journalctl -u keycloak | grep "Profile prod activated"
```

---

### **6.4 TLS / HTTPS & Reverse Proxy Readiness**

#### TLS / HTTPS (Optional)

```yaml
keycloak_https_enabled: false
keycloak_https_port: 8443
keycloak_tls_cert_file: /etc/keycloak/tls/tls.crt
keycloak_tls_key_file: /etc/keycloak/tls/tls.key
```

> Certificate management is intentionally external.

#### Reverse Proxy Support

```yaml
keycloak_proxy_mode: edge
keycloak_hostname: auth.example.com
hostname-strict=false
```
---

## **7. Execution Using Ansible**

```bash
ansible-playbook -i inventory.ini playbook.yml
```

Expected result:

```
failed=0
```

---

## **8. Validation & Health Checks**

```bash
sudo systemctl status keycloak
```

```bash
sudo ss -lntp | grep 8080
```

```bash
curl -I http://localhost:8080
```

Admin Console:

```
https://<hostname>:8443/admin/master/console
```

---

## **9. Troubleshooting**

| Issue           | Cause                  | Resolution        |
| --------------- | ---------------------- | ----------------- |
| HTTPS required  | Prod mode enabled      | Enable TLS        |
| Redirect loop   | Proxy misconfiguration | Verify proxy mode |
| DB auth failure | Auth mismatch          | Check DB config   |
| Restart loop    | Invalid config         | Review config     |

---

## **10. Best Practices Implemented**

| Practice           | Description            |
| ------------------ | ---------------------- |
| Non-root execution | `keycloak` system user |
| Idempotent design  | Safe re-runs           |
| Version pinning    | Controlled upgrades    |
| Modern runtime     | Java 17+, Quarkus      |
| Secure defaults    | No dev mode            |
| Modular TLS        | Optional               |
| Proxy awareness    | Production-ready       |

---

## **11. Conclusion**

This Ansible role delivers a **clean, reproducible, production-ready** Keycloak deployment that:

* Supports **latest Keycloak releases**
* Enforces **modern OS & Java compatibility**
* Enables **TLS / HTTPS and reverse proxy support as options**

---

## **12. References**

| Reference                                                                                      | Description    |
| ---------------------------------------------------------------------------------------------- | -------------- |
| [https://www.keycloak.org/documentation](https://www.keycloak.org/documentation)               | Official Docs  |
| [https://www.keycloak.org/server/configuration](https://www.keycloak.org/server/configuration) | Quarkus Config |
| [https://www.keycloak.org/server/reverseproxy](https://www.keycloak.org/server/reverseproxy)   | Reverse Proxy  |
| [https://github.com/keycloak/keycloak/releases](https://github.com/keycloak/keycloak/releases) | Releases       |

---
