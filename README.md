# factorio Installation Guide

factorio is a free and open-source Factorio dedicated server. Dedicated server for Factorio factory building game

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
  - CPU: 2+ cores
  - RAM: 1GB minimum
  - Storage: 5GB for saves
  - Network: Game protocol
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 34197 (default factorio port)
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

# Install factorio
sudo dnf install -y factorio

# Enable and start service
sudo systemctl enable --now factorio

# Configure firewall
sudo firewall-cmd --permanent --add-port=34197/tcp
sudo firewall-cmd --reload

# Verify installation
factorio --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install factorio
sudo apt install -y factorio

# Enable and start service
sudo systemctl enable --now factorio

# Configure firewall
sudo ufw allow 34197

# Verify installation
factorio --version
```

### Arch Linux

```bash
# Install factorio
sudo pacman -S factorio

# Enable and start service
sudo systemctl enable --now factorio

# Verify installation
factorio --version
```

### Alpine Linux

```bash
# Install factorio
apk add --no-cache factorio

# Enable and start service
rc-update add factorio default
rc-service factorio start

# Verify installation
factorio --version
```

### openSUSE/SLES

```bash
# Install factorio
sudo zypper install -y factorio

# Enable and start service
sudo systemctl enable --now factorio

# Configure firewall
sudo firewall-cmd --permanent --add-port=34197/tcp
sudo firewall-cmd --reload

# Verify installation
factorio --version
```

### macOS

```bash
# Using Homebrew
brew install factorio

# Start service
brew services start factorio

# Verify installation
factorio --version
```

### FreeBSD

```bash
# Using pkg
pkg install factorio

# Enable in rc.conf
echo 'factorio_enable="YES"' >> /etc/rc.conf

# Start service
service factorio start

# Verify installation
factorio --version
```

### Windows

```bash
# Using Chocolatey
choco install factorio

# Or using Scoop
scoop install factorio

# Verify installation
factorio --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/factorio

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
factorio --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable factorio

# Start service
sudo systemctl start factorio

# Stop service
sudo systemctl stop factorio

# Restart service
sudo systemctl restart factorio

# Check status
sudo systemctl status factorio

# View logs
sudo journalctl -u factorio -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add factorio default

# Start service
rc-service factorio start

# Stop service
rc-service factorio stop

# Restart service
rc-service factorio restart

# Check status
rc-service factorio status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'factorio_enable="YES"' >> /etc/rc.conf

# Start service
service factorio start

# Stop service
service factorio stop

# Restart service
service factorio restart

# Check status
service factorio status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start factorio
brew services stop factorio
brew services restart factorio

# Check status
brew services list | grep factorio
```

### Windows Service Manager

```powershell
# Start service
net start factorio

# Stop service
net stop factorio

# Using PowerShell
Start-Service factorio
Stop-Service factorio
Restart-Service factorio

# Check status
Get-Service factorio
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream factorio_backend {
    server 127.0.0.1:34197;
}

server {
    listen 80;
    server_name factorio.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name factorio.example.com;

    ssl_certificate /etc/ssl/certs/factorio.example.com.crt;
    ssl_certificate_key /etc/ssl/private/factorio.example.com.key;

    location / {
        proxy_pass http://factorio_backend;
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
    ServerName factorio.example.com
    Redirect permanent / https://factorio.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName factorio.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/factorio.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/factorio.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:34197/
    ProxyPassReverse / http://127.0.0.1:34197/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend factorio_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/factorio.pem
    redirect scheme https if !{ ssl_fc }
    default_backend factorio_backend

backend factorio_backend
    balance roundrobin
    server factorio1 127.0.0.1:34197 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R factorio:factorio /etc/factorio
sudo chmod 750 /etc/factorio

# Configure firewall
sudo firewall-cmd --permanent --add-port=34197/tcp
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
sudo systemctl status factorio

# View logs
sudo journalctl -u factorio -f

# Monitor resource usage
top -p $(pgrep factorio)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/factorio"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/factorio-backup-$DATE.tar.gz" /etc/factorio /var/lib/factorio

echo "Backup completed: $BACKUP_DIR/factorio-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop factorio

# Restore from backup
tar -xzf /backup/factorio/factorio-backup-*.tar.gz -C /

# Start service
sudo systemctl start factorio
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u factorio -n 100
sudo tail -f /var/log/factorio/factorio.log

# Check configuration
factorio --version

# Check permissions
ls -la /etc/factorio
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 34197

# Test connectivity
telnet localhost 34197

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep factorio)

# Check disk I/O
iotop -p $(pgrep factorio)

# Check connections
ss -an | grep 34197
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  factorio:
    image: factorio:latest
    ports:
      - "34197:34197"
    volumes:
      - ./config:/etc/factorio
      - ./data:/var/lib/factorio
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update factorio

# Debian/Ubuntu
sudo apt update && sudo apt upgrade factorio

# Arch Linux
sudo pacman -Syu factorio

# Alpine Linux
apk update && apk upgrade factorio

# openSUSE
sudo zypper update factorio

# FreeBSD
pkg update && pkg upgrade factorio

# Always backup before updates
tar -czf /backup/factorio-pre-update-$(date +%Y%m%d).tar.gz /etc/factorio

# Restart after updates
sudo systemctl restart factorio
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/factorio

# Clean old logs
find /var/log/factorio -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/factorio
```

## Additional Resources

- Official Documentation: https://docs.factorio.org/
- GitHub Repository: https://github.com/factorio/factorio
- Community Forum: https://forum.factorio.org/
- Best Practices Guide: https://docs.factorio.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
