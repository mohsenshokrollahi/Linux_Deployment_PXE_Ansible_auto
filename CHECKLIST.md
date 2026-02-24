# Deployment Checklist

## Pre-Deployment
- [ ] DHCP server configured with PXE options (next-server, filename)
- [ ] DNS server configured for all 50 servers
- [ ] TFTP server installed and running
- [ ] HTTP/Apache server installed and running
- [ ] Ubuntu netboot files downloaded
- [ ] All 50 servers connected to network
- [ ] Server BIOS/UEFI configured for PXE boot

## PXE Server Configuration
- [ ] Install required packages (tftpd-hpa, apache2, syslinux)
- [ ] Copy PXE boot files to /srv/tftp/
- [ ] Extract Ubuntu netboot files
- [ ] Copy preseed.cfg to /var/www/html/
- [ ] Update PXE_SERVER_IP in pxe/default file
- [ ] Copy pxe/default to /srv/tftp/pxelinux.cfg/
- [ ] Verify TFTP service is running
- [ ] Verify Apache service is running

## Deployment Execution
- [ ] Power on all 50 servers
- [ ] Verify servers boot via PXE
- [ ] Monitor installation progress
- [ ] Wait for all servers to complete installation and reboot

## Post-Deployment
- [ ] Update ansible/inventory.ini with actual server IPs
- [ ] Test SSH connectivity to one server manually
- [ ] Run verification playbook: `ansible-playbook -i inventory.ini verify.yml`
- [ ] Run post-install playbook: `ansible-playbook -i inventory.ini post_install.yml`
- [ ] Verify all servers are accessible
- [ ] Change default passwords
- [ ] Configure SSH keys for passwordless authentication

## Security Hardening (Recommended)
- [ ] Disable root password login (use SSH keys only)
- [ ] Configure firewall (ufw)
- [ ] Set up centralized logging
- [ ] Configure NTP for time synchronization
- [ ] Apply security updates
