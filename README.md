# obs-studio Installation Guide

obs-studio is a free and open-source broadcasting software. OBS Studio provides video recording and streaming

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
  - CPU: 4+ cores
  - RAM: 4GB minimum
  - Storage: 20GB for recordings
  - Network: GUI application
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port N/A (default obs-studio port)
  - RTMP output
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

# Install obs-studio
sudo dnf install -y obs_studio

# Enable and start service
sudo systemctl enable --now obs-studio

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
obs-studio --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install obs-studio
sudo apt install -y obs_studio

# Enable and start service
sudo systemctl enable --now obs-studio

# Configure firewall
sudo ufw allow N/A

# Verify installation
obs-studio --version
```

### Arch Linux

```bash
# Install obs-studio
sudo pacman -S obs_studio

# Enable and start service
sudo systemctl enable --now obs-studio

# Verify installation
obs-studio --version
```

### Alpine Linux

```bash
# Install obs-studio
apk add --no-cache obs_studio

# Enable and start service
rc-update add obs-studio default
rc-service obs-studio start

# Verify installation
obs-studio --version
```

### openSUSE/SLES

```bash
# Install obs-studio
sudo zypper install -y obs_studio

# Enable and start service
sudo systemctl enable --now obs-studio

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
obs-studio --version
```

### macOS

```bash
# Using Homebrew
brew install obs_studio

# Start service
brew services start obs_studio

# Verify installation
obs-studio --version
```

### FreeBSD

```bash
# Using pkg
pkg install obs_studio

# Enable in rc.conf
echo 'obs-studio_enable="YES"' >> /etc/rc.conf

# Start service
service obs-studio start

# Verify installation
obs-studio --version
```

### Windows

```bash
# Using Chocolatey
choco install obs_studio

# Or using Scoop
scoop install obs_studio

# Verify installation
obs-studio --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/obs_studio

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
obs-studio --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable obs-studio

# Start service
sudo systemctl start obs-studio

# Stop service
sudo systemctl stop obs-studio

# Restart service
sudo systemctl restart obs-studio

# Check status
sudo systemctl status obs-studio

# View logs
sudo journalctl -u obs-studio -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add obs-studio default

# Start service
rc-service obs-studio start

# Stop service
rc-service obs-studio stop

# Restart service
rc-service obs-studio restart

# Check status
rc-service obs-studio status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'obs-studio_enable="YES"' >> /etc/rc.conf

# Start service
service obs-studio start

# Stop service
service obs-studio stop

# Restart service
service obs-studio restart

# Check status
service obs-studio status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start obs_studio
brew services stop obs_studio
brew services restart obs_studio

# Check status
brew services list | grep obs_studio
```

### Windows Service Manager

```powershell
# Start service
net start obs-studio

# Stop service
net stop obs-studio

# Using PowerShell
Start-Service obs-studio
Stop-Service obs-studio
Restart-Service obs-studio

# Check status
Get-Service obs-studio
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream obs_studio_backend {
    server 127.0.0.1:N/A;
}

server {
    listen 80;
    server_name obs_studio.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name obs_studio.example.com;

    ssl_certificate /etc/ssl/certs/obs_studio.example.com.crt;
    ssl_certificate_key /etc/ssl/private/obs_studio.example.com.key;

    location / {
        proxy_pass http://obs_studio_backend;
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
    ServerName obs_studio.example.com
    Redirect permanent / https://obs_studio.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName obs_studio.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/obs_studio.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/obs_studio.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:N/A/
    ProxyPassReverse / http://127.0.0.1:N/A/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend obs_studio_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/obs_studio.pem
    redirect scheme https if !{ ssl_fc }
    default_backend obs_studio_backend

backend obs_studio_backend
    balance roundrobin
    server obs_studio1 127.0.0.1:N/A check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R obs_studio:obs_studio /etc/obs_studio
sudo chmod 750 /etc/obs_studio

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
sudo systemctl status obs-studio

# View logs
sudo journalctl -u obs-studio -f

# Monitor resource usage
top -p $(pgrep obs_studio)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/obs_studio"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/obs_studio-backup-$DATE.tar.gz" /etc/obs_studio /var/lib/obs_studio

echo "Backup completed: $BACKUP_DIR/obs_studio-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop obs-studio

# Restore from backup
tar -xzf /backup/obs_studio/obs_studio-backup-*.tar.gz -C /

# Start service
sudo systemctl start obs-studio
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u obs-studio -n 100
sudo tail -f /var/log/obs_studio/obs_studio.log

# Check configuration
obs-studio --version

# Check permissions
ls -la /etc/obs_studio
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
top -p $(pgrep obs_studio)

# Check disk I/O
iotop -p $(pgrep obs_studio)

# Check connections
ss -an | grep N/A
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  obs_studio:
    image: obs_studio:latest
    ports:
      - "N/A:N/A"
    volumes:
      - ./config:/etc/obs_studio
      - ./data:/var/lib/obs_studio
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update obs_studio

# Debian/Ubuntu
sudo apt update && sudo apt upgrade obs_studio

# Arch Linux
sudo pacman -Syu obs_studio

# Alpine Linux
apk update && apk upgrade obs_studio

# openSUSE
sudo zypper update obs_studio

# FreeBSD
pkg update && pkg upgrade obs_studio

# Always backup before updates
tar -czf /backup/obs_studio-pre-update-$(date +%Y%m%d).tar.gz /etc/obs_studio

# Restart after updates
sudo systemctl restart obs-studio
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/obs_studio

# Clean old logs
find /var/log/obs_studio -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/obs_studio
```

## Additional Resources

- Official Documentation: https://docs.obs_studio.org/
- GitHub Repository: https://github.com/obs_studio/obs_studio
- Community Forum: https://forum.obs_studio.org/
- Best Practices Guide: https://docs.obs_studio.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
