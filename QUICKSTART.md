# Quick Start - How to Run

## Step 1: Setup PXE Server (Run on your PXE server - Linux)

```bash
# Install packages
sudo apt update
sudo apt install -y tftpd-hpa apache2 syslinux pxelinux

# Copy PXE files
sudo cp /usr/lib/PXELINUX/pxelinux.0 /srv/tftp/
sudo cp /usr/lib/syslinux/modules/bios/{ldlinux.c32,libcom32.c32,libutil.c32,vesamenu.c32} /srv/tftp/

# Download Ubuntu netboot
cd /tmp
wget http://archive.ubuntu.com/ubuntu/dists/jammy/main/installer-amd64/current/legacy-images/netboot/netboot.tar.gz
sudo tar -xzf netboot.tar.gz -C /srv/tftp/

# Create config directory
sudo mkdir -p /srv/tftp/pxelinux.cfg

# Copy your files (upload pxe/default and pxe/preseed.cfg to PXE server first)
sudo cp default /srv/tftp/pxelinux.cfg/
sudo cp preseed.cfg /var/www/html/
sudo chmod 644 /var/www/html/preseed.cfg

# Start services
sudo systemctl restart tftpd-hpa
sudo systemctl restart apache2
```

## Step 2: Update Configuration Files

**Edit pxe/default** - Replace `<PXE_SERVER_IP>` with your actual IP:
```
url=http://192.168.1.100/preseed.cfg
```

**Edit ansible/inventory.ini** - Add your 50 server IPs:
```ini
server01 ansible_host=192.168.1.101
server02 ansible_host=192.168.1.102
# ... add all 50 servers
```

## Step 3: Boot Servers
- Power on all 50 servers
- They will automatically install Ubuntu via PXE
- Wait 15-30 minutes for installation to complete

## Step 4: Run Ansible (Run from your Ansible control machine)

```bash
cd C:\Users\mshokr\Documents\Automation\Linux_Deployment\ansible

# Test connectivity
ansible -i inventory.ini ubuntu_servers -m ping

# Verify deployment
ansible-playbook -i inventory.ini verify.yml

# Run post-installation
ansible-playbook -i inventory.ini post_install.yml
```

## Done!
All 50 servers are now deployed with Ubuntu and SSH enabled.
