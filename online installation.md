**Step-by-step guide** for **installing and using the Red Hat Discovery Tool on RHEL 9.5 (connected/online environment)**:

---

## ✅ **1. Prerequisites**
- System **must have internet access**.
- Ensure hardware/software requirements are met.
- You need **`sudo` access**.
- Podman will be used, but Discovery must **not run as root**.

---

## 🛠️ **2. Install Discovery Tool**

### a. **Enable Discovery Repository**
```bash
sudo subscription-manager repos --enable=discovery-1-for-rhel-9-x86_64-rpms
```

### b. **Install the Discovery Installer**
```bash
sudo dnf install -y discovery-installer
```

---

## 🔐 **3. Authenticate and Install**

### a. **Login to Red Hat Registry**
```bash
podman login registry.redhat.io
```

### b. **Run the Installer**
```bash
discovery-installer install
```
> ⚠️ You’ll be prompted to set a **Discovery server password** during this step.

---

## 🚀 **4. Start the Discovery Application**
```bash
systemctl --user start discovery-app
```

### (Optional) **Monitor First Startup**
```bash
systemctl --user status discovery-app
podman ps -a
journalctl -t discovery-app
journalctl -t discovery-server
podman logs --since 1m -f discovery-server
```

---

## 🔑 **5. Change Discovery Admin Password (Optional but Recommended)**
```bash
discovery-installer create-server-password
```

Then restart Discovery:
```bash
systemctl --user restart discovery-app
```
> ❗ Password must be strong: 10+ chars, no dictionary words, not numeric-only.

---

## ⚙️ **6. Post-Installation Configuration (Optional)**

### a. **Enable Persistent User Sessions**
```bash
loginctl enable-linger $USER
```

### b. **Allow External Access**
If accessing from another machine on the same network:
- **Open TCP port 9443** in firewall:
```bash
sudo firewall-cmd --add-port=9443/tcp --permanent
sudo firewall-cmd --reload
```

---
