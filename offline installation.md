 **step-by-step summary** for installing and using **Red Hat Discovery in a disconnected (offline) environment**:

---

## ✅ **1. Prerequisites**
- One system **with internet access (connected system)**.
- One **offline system (disconnected)** where Discovery will be installed.
- Both systems must be able to install packages with `dnf`.
- You need `sudo` permissions, and **Discovery must not run as root**.

---

## 🌐 **2. On the Connected System**

### a. **Log in to Red Hat Container Registry**
```bash
podman login registry.redhat.io
```

### b. **Download Container Images**
```bash
podman pull registry.redhat.io/discovery/discovery-ui-rhel9:1.13 \
             registry.redhat.io/discovery/discovery-server-rhel9:1.13 \
             registry.redhat.io/rhel9/postgresql-15:latest \
             registry.redhat.io/rhel9/redis-6:latest
```

### c. **Save Images into One File**
```bash
podman save --multi-image-archive \
             registry.redhat.io/discovery/discovery-ui-rhel9:1.13 \
             registry.redhat.io/discovery/discovery-server-rhel9:1.13 \
             registry.redhat.io/rhel9/postgresql-15:latest \
             registry.redhat.io/rhel9/redis-6:latest \
             | gzip > discovery-images.tar.gz
```

### d. **Transfer File**
- Use USB, SCP, or any offline method to transfer `discovery-images.tar.gz` to the **disconnected system**.

---

## 🔌 **3. On the Disconnected System**

### a. **Load Images into Podman**
```bash
podman load -i discovery-images.tar.gz
```

### b. **Enable Repository & Install Installer**
```bash
sudo subscription-manager repos --enable=discovery-1-for-rhel-9-x86_64-rpms
sudo dnf install -y discovery-installer
```

### c. **Run Installer & Set Admin Password**
```bash
discovery-installer install
```
> ⚠️ Will prompt for a server password.

---

## 🚀 **4. Start Discovery**
```bash
systemctl --user start discovery-app
```

### (Optional) **Monitor Startup Logs**
```bash
systemctl --user status discovery-app
podman ps -a
journalctl -t discovery-app
journalctl -t discovery-server
podman logs --since 1m -f discovery-server
```

---

## 🔑 **5. Change Discovery Admin Password (Optional)**
```bash
discovery-installer create-server-password
```

### Restart Discovery to Apply Changes:
```bash
systemctl --user restart discovery-app
```

---

## ⚙️ **6. Optional Post-Install Settings**

### a. **Enable Persistent User Sessions**
```bash
loginctl enable-linger $USER
```

### b. **Open Port 9443 for Remote Access**
```bash
sudo firewall-cmd --add-port=9443/tcp --permanent
sudo firewall-cmd --reload
```

---

