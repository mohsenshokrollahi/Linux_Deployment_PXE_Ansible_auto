# Ubuntu PXE Deployment with Ansible

## Overview
Automated Ubuntu deployment for 50 servers using PXE boot and Ansible post-configuration.

## Prerequisites
- DHCP server configured with PXE boot options
- DNS server configured
- TFTP server for PXE files
- HTTP/NFS server for Ubuntu installation files
- Ansible control node

## Deployment Steps

### 1. PXE Server Setup
```bash
# Install required packages
sudo apt update
sudo apt install -y tftpd-hpa apache2 syslinux pxelinux

# Copy PXE boot files
sudo cp /usr/lib/PXELINUX/pxelinux.0 /srv/tftp/
sudo cp /usr/lib/syslinux/modules/bios/{ldlinux.c32,libcom32.c32,libutil.c32,vesamenu.c32} /srv/tftp/

# Create PXE config directory
sudo mkdir -p /srv/tftp/pxelinux.cfg
```

### 2. Download Ubuntu Netboot Files
```bash
# Download Ubuntu 22.04 netboot files
cd /tmp
wget http://archive.ubuntu.com/ubuntu/dists/jammy/main/installer-amd64/current/legacy-images/netboot/netboot.tar.gz
sudo tar -xzf netboot.tar.gz -C /srv/tftp/
```

### 3. Configure DHCP Server
Add to your DHCP configuration:
```
next-server <PXE_SERVER_IP>;
filename "pxelinux.0";
```

### 4. Setup HTTP Server for Preseed
```bash
# Copy preseed file to web root
sudo cp pxe/preseed.cfg /var/www/html/
sudo chmod 644 /var/www/html/preseed.cfg
```

### 5. Deploy PXE Configuration
```bash
sudo cp pxe/default /srv/tftp/pxelinux.cfg/
```

### 6. Boot Servers via PXE
- Configure servers to boot from network (PXE)
- Servers will automatically install Ubuntu

### 7. Post-Installation with Ansible
```bash
cd ansible
# Update inventory with server IPs
ansible-playbook -i inventory.ini post_install.yml
```

## Files Structure
```
Linux_Deployment/
├── README.md
├── pxe/
│   ├── default              # PXE boot menu
│   └── preseed.cfg          # Ubuntu autoinstall config
└── ansible/
    ├── inventory.ini        # Server inventory
    ├── post_install.yml     # Post-install playbook
    └── ansible.cfg          # Ansible configuration
```

## Notes
- Default root password: Change123! (change after deployment)
- SSH enabled by default on port 22
- Servers will reboot automatically after installation
