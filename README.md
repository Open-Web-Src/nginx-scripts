# Install & Configure Nginx from Source on AWS EC2

This guide explains how to install and configure Nginx from source on an EC2 instance.

## 📌 Prerequisites

- Amazon Linux 2, Ubuntu, or RHEL-based EC2 instance
- SSH access to the EC2 instance
- `sudo` permissions

## 🚀 Step 1: Update the System

```sh
yum update -y   # Amazon Linux / RHEL
apt update -y   # Ubuntu/Debian
```

## 🔧 Step 2: Install Required Dependencies

```sh
yum install -y gcc pcre pcre-devel zlib zlib-devel make openssl openssl-devel  # Amazon Linux
apt install -y gcc libpcre3-dev zlib1g-dev make libssl-dev   # Ubuntu
```

## 📥 Step 3: Download and Extract Nginx Source Code

```sh
wget http://nginx.org/download/nginx-1.24.0.tar.gz
tar -xzvf nginx-1.24.0.tar.gz
cd nginx-1.24.0
```

## ⚙️ Step 4: Configure Nginx

```sh
./configure \
    --sbin-path=/usr/bin/nginx \
    --conf-path=/etc/nginx/nginx.conf \
    --error-log-path=/var/log/nginx/error.log \
    --http-log-path=/var/log/nginx/access.log \
    --pid-path=/var/run/nginx.pid \
    --with-pcre \
    --with-http_ssl_module
```

## 📥 🏗️ Step 5: Compile and Install Nginx

```sh
make
make install
```

## 🚀 Step 6: Create a Systemd Service for Nginx

### 1️⃣ Create the Systemd Service File

```sh
nano /etc/systemd/system/nginx.service
```

If you want to make the service available system-wide, place it under `/lib/systemd/system/nginx.service` instead of `/etc/systemd/system/nginx.service`.

### 2️⃣ Add the following content and save the file

```sh
[Unit]
Description=Nginx - High Performance Web Server
After=network.target

[Service]
Type=forking
ExecStartPre=/usr/bin/nginx -t
ExecStart=/usr/bin/nginx
ExecReload=/usr/bin/nginx -s reload
ExecStop=/usr/bin/nginx -s stop
PIDFile=/var/run/nginx.pid
LimitNOFILE=65535

# Run as root (or use a different user if configured)
User=root
Group=root

[Install]
WantedBy=multi-user.target
```

### 3️⃣ Reload Systemd, Enable, and Start Nginx

```sh
systemctl daemon-reload
systemctl enable nginx
systemctl start nginx
systemctl status nginx
```

## ✅ Step 7: Verify Installation

```sh
nginx -V  # Should return the installed version
```
