# typesense Installation Guide

typesense is a free and open-source search engine. Typesense provides fast search engine with built-in typo tolerance

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 2GB minimum
  - Storage: 10GB for data
  - Network: HTTP/REST API
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8108 (default typesense port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install typesense
sudo dnf install -y typesense

# Enable and start service
sudo systemctl enable --now typesense

# Configure firewall
sudo firewall-cmd --permanent --add-port=8108/tcp
sudo firewall-cmd --reload

# Verify installation
typesense --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install typesense
sudo apt install -y typesense

# Enable and start service
sudo systemctl enable --now typesense

# Configure firewall
sudo ufw allow 8108

# Verify installation
typesense --version
```

### Arch Linux

```bash
# Install typesense
sudo pacman -S typesense

# Enable and start service
sudo systemctl enable --now typesense

# Verify installation
typesense --version
```

### Alpine Linux

```bash
# Install typesense
apk add --no-cache typesense

# Enable and start service
rc-update add typesense default
rc-service typesense start

# Verify installation
typesense --version
```

### openSUSE/SLES

```bash
# Install typesense
sudo zypper install -y typesense

# Enable and start service
sudo systemctl enable --now typesense

# Configure firewall
sudo firewall-cmd --permanent --add-port=8108/tcp
sudo firewall-cmd --reload

# Verify installation
typesense --version
```

### macOS

```bash
# Using Homebrew
brew install typesense

# Start service
brew services start typesense

# Verify installation
typesense --version
```

### FreeBSD

```bash
# Using pkg
pkg install typesense

# Enable in rc.conf
echo 'typesense_enable="YES"' >> /etc/rc.conf

# Start service
service typesense start

# Verify installation
typesense --version
```

### Windows

```bash
# Using Chocolatey
choco install typesense

# Or using Scoop
scoop install typesense

# Verify installation
typesense --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/typesense

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
typesense --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable typesense

# Start service
sudo systemctl start typesense

# Stop service
sudo systemctl stop typesense

# Restart service
sudo systemctl restart typesense

# Check status
sudo systemctl status typesense

# View logs
sudo journalctl -u typesense -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add typesense default

# Start service
rc-service typesense start

# Stop service
rc-service typesense stop

# Restart service
rc-service typesense restart

# Check status
rc-service typesense status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'typesense_enable="YES"' >> /etc/rc.conf

# Start service
service typesense start

# Stop service
service typesense stop

# Restart service
service typesense restart

# Check status
service typesense status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start typesense
brew services stop typesense
brew services restart typesense

# Check status
brew services list | grep typesense
```

### Windows Service Manager

```powershell
# Start service
net start typesense

# Stop service
net stop typesense

# Using PowerShell
Start-Service typesense
Stop-Service typesense
Restart-Service typesense

# Check status
Get-Service typesense
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream typesense_backend {
    server 127.0.0.1:8108;
}

server {
    listen 80;
    server_name typesense.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name typesense.example.com;

    ssl_certificate /etc/ssl/certs/typesense.example.com.crt;
    ssl_certificate_key /etc/ssl/private/typesense.example.com.key;

    location / {
        proxy_pass http://typesense_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName typesense.example.com
    Redirect permanent / https://typesense.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName typesense.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/typesense.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/typesense.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8108/
    ProxyPassReverse / http://127.0.0.1:8108/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend typesense_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/typesense.pem
    redirect scheme https if !{ ssl_fc }
    default_backend typesense_backend

backend typesense_backend
    balance roundrobin
    server typesense1 127.0.0.1:8108 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R typesense:typesense /etc/typesense
sudo chmod 750 /etc/typesense

# Configure firewall
sudo firewall-cmd --permanent --add-port=8108/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status typesense

# View logs
sudo journalctl -u typesense -f

# Monitor resource usage
top -p $(pgrep typesense)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/typesense"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/typesense-backup-$DATE.tar.gz" /etc/typesense /var/lib/typesense

echo "Backup completed: $BACKUP_DIR/typesense-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop typesense

# Restore from backup
tar -xzf /backup/typesense/typesense-backup-*.tar.gz -C /

# Start service
sudo systemctl start typesense
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u typesense -n 100
sudo tail -f /var/log/typesense/typesense.log

# Check configuration
typesense --version

# Check permissions
ls -la /etc/typesense
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8108

# Test connectivity
telnet localhost 8108

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep typesense)

# Check disk I/O
iotop -p $(pgrep typesense)

# Check connections
ss -an | grep 8108
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  typesense:
    image: typesense:latest
    ports:
      - "8108:8108"
    volumes:
      - ./config:/etc/typesense
      - ./data:/var/lib/typesense
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update typesense

# Debian/Ubuntu
sudo apt update && sudo apt upgrade typesense

# Arch Linux
sudo pacman -Syu typesense

# Alpine Linux
apk update && apk upgrade typesense

# openSUSE
sudo zypper update typesense

# FreeBSD
pkg update && pkg upgrade typesense

# Always backup before updates
tar -czf /backup/typesense-pre-update-$(date +%Y%m%d).tar.gz /etc/typesense

# Restart after updates
sudo systemctl restart typesense
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/typesense

# Clean old logs
find /var/log/typesense -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/typesense
```

## Additional Resources

- Official Documentation: https://docs.typesense.org/
- GitHub Repository: https://github.com/typesense/typesense
- Community Forum: https://forum.typesense.org/
- Best Practices Guide: https://docs.typesense.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
