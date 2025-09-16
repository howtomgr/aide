# aide Installation Guide

aide is a free and open-source intrusion detection. AIDE provides file integrity checker and intrusion detection

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
  - RAM: 256MB minimum
  - Storage: 1GB for DB
  - Network: Local only
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port N/A (default aide port)
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

# Install aide
sudo dnf install -y aide

# Enable and start service
sudo systemctl enable --now aide

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
aide --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install aide
sudo apt install -y aide

# Enable and start service
sudo systemctl enable --now aide

# Configure firewall
sudo ufw allow N/A

# Verify installation
aide --version
```

### Arch Linux

```bash
# Install aide
sudo pacman -S aide

# Enable and start service
sudo systemctl enable --now aide

# Verify installation
aide --version
```

### Alpine Linux

```bash
# Install aide
apk add --no-cache aide

# Enable and start service
rc-update add aide default
rc-service aide start

# Verify installation
aide --version
```

### openSUSE/SLES

```bash
# Install aide
sudo zypper install -y aide

# Enable and start service
sudo systemctl enable --now aide

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
aide --version
```

### macOS

```bash
# Using Homebrew
brew install aide

# Start service
brew services start aide

# Verify installation
aide --version
```

### FreeBSD

```bash
# Using pkg
pkg install aide

# Enable in rc.conf
echo 'aide_enable="YES"' >> /etc/rc.conf

# Start service
service aide start

# Verify installation
aide --version
```

### Windows

```bash
# Using Chocolatey
choco install aide

# Or using Scoop
scoop install aide

# Verify installation
aide --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/aide

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
aide --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable aide

# Start service
sudo systemctl start aide

# Stop service
sudo systemctl stop aide

# Restart service
sudo systemctl restart aide

# Check status
sudo systemctl status aide

# View logs
sudo journalctl -u aide -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add aide default

# Start service
rc-service aide start

# Stop service
rc-service aide stop

# Restart service
rc-service aide restart

# Check status
rc-service aide status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'aide_enable="YES"' >> /etc/rc.conf

# Start service
service aide start

# Stop service
service aide stop

# Restart service
service aide restart

# Check status
service aide status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start aide
brew services stop aide
brew services restart aide

# Check status
brew services list | grep aide
```

### Windows Service Manager

```powershell
# Start service
net start aide

# Stop service
net stop aide

# Using PowerShell
Start-Service aide
Stop-Service aide
Restart-Service aide

# Check status
Get-Service aide
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream aide_backend {
    server 127.0.0.1:N/A;
}

server {
    listen 80;
    server_name aide.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name aide.example.com;

    ssl_certificate /etc/ssl/certs/aide.example.com.crt;
    ssl_certificate_key /etc/ssl/private/aide.example.com.key;

    location / {
        proxy_pass http://aide_backend;
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
    ServerName aide.example.com
    Redirect permanent / https://aide.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName aide.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/aide.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/aide.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:N/A/
    ProxyPassReverse / http://127.0.0.1:N/A/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend aide_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/aide.pem
    redirect scheme https if !{ ssl_fc }
    default_backend aide_backend

backend aide_backend
    balance roundrobin
    server aide1 127.0.0.1:N/A check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R aide:aide /etc/aide
sudo chmod 750 /etc/aide

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
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
sudo systemctl status aide

# View logs
sudo journalctl -u aide -f

# Monitor resource usage
top -p $(pgrep aide)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/aide"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/aide-backup-$DATE.tar.gz" /etc/aide /var/lib/aide

echo "Backup completed: $BACKUP_DIR/aide-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop aide

# Restore from backup
tar -xzf /backup/aide/aide-backup-*.tar.gz -C /

# Start service
sudo systemctl start aide
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u aide -n 100
sudo tail -f /var/log/aide/aide.log

# Check configuration
aide --version

# Check permissions
ls -la /etc/aide
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep N/A

# Test connectivity
telnet localhost N/A

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep aide)

# Check disk I/O
iotop -p $(pgrep aide)

# Check connections
ss -an | grep N/A
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  aide:
    image: aide:latest
    ports:
      - "N/A:N/A"
    volumes:
      - ./config:/etc/aide
      - ./data:/var/lib/aide
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update aide

# Debian/Ubuntu
sudo apt update && sudo apt upgrade aide

# Arch Linux
sudo pacman -Syu aide

# Alpine Linux
apk update && apk upgrade aide

# openSUSE
sudo zypper update aide

# FreeBSD
pkg update && pkg upgrade aide

# Always backup before updates
tar -czf /backup/aide-pre-update-$(date +%Y%m%d).tar.gz /etc/aide

# Restart after updates
sudo systemctl restart aide
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/aide

# Clean old logs
find /var/log/aide -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/aide
```

## Additional Resources

- Official Documentation: https://docs.aide.org/
- GitHub Repository: https://github.com/aide/aide
- Community Forum: https://forum.aide.org/
- Best Practices Guide: https://docs.aide.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
