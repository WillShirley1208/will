---
title: linux A vs B
date: 2025-01-22 14:11:11
tags: A vs B
categories: linux
---

# Systemd limits vs PAM limits

**Systemd limits** and **PAM limits** are two different ways of configuring resource limits in Linux. They control aspects like **open file limits (**nofile**)**, **memory**, and **CPU usage**, but they work at different levels of the system.

## PAM Limits

PAM (Pluggable Authentication Module) sets limits when **a user logs in** via **SSH, TTY, or GUI login**. It does **not** apply to system services. 

**Configuration:**

- /etc/security/limits.conf

- /etc/security/limits.d/*.conf

```shell
# Example (/etc/security/limits.conf or /etc/security/limits.d/custom.conf):**
dongw soft nofile 65535
dongw hard nofile 65535
```

**Limitations of PAM:**

- Only affects interactive logins (TTY, SSH, GUI).

- Does **not** apply to systemd-managed services.

## Systemd Limits

Systemd sets limits **for system services and user sessions**. It applies to **both login sessions and system services**, making it more powerful than PAM.

**Configuration:**

- **Global (for all services and users)**:

- /etc/systemd/system.conf (system-wide limits)

- /etc/systemd/user.conf (per-user session limits)

- **Per-service limits** (for specific services):

- /etc/systemd/system/<service>.service

### Global Limits

Applies to **all** processes managed by systemd. (/etc/systemd/system.conf or /etc/systemd/user.conf)

```shell
DefaultLimitNOFILE=65535
```

### Per-Service Limits

 This applies to only on specify service. (e.g. /etc/systemd/system/myservice.service)

```shell
[Service]
LimitNOFILE=65535
```

**Advantages of Systemd Limits:**

- Affects **both user logins and system services**.

- Ensures limits persist across **system reboots**.

- More reliable for applications like Ceph, Docker, and databases.

## **Comparison Table**

**Feature** | **PAM Limits (`limits.conf`)** | **Systemd Limits (`system.conf`/`.service`)**
:----------|-----------------------------------|-------------------------------------------
Applies to interactive users | ✅ Yes (SSH, TTY, GUI) | ✅ Yes
Applies to system services | ❌ No | ✅ Yes
Persists across reboots | ✅ Yes | ✅ Yes
Easy to configure | ✅ Yes | ✅ Yes
Best for tuning servers | ⚠️  Partial | ✅ Yes

- **For interactive user sessions** → Use **PAM (**limits.conf**)**.

- **For system services and all users** → Use **Systemd (**system.conf**,** .service**)**.

- **For the best persistence and reliability**, configure **both**.