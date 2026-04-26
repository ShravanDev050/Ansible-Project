# AD Integration Role

An Ansible role for integrating Windows and Linux systems into existing Active Directory domains.

## Overview

This role automates the integration of servers and workstations into an existing Active Directory environment:
- **Windows**: Domain join using native Windows domain management tools
- **Linux**: Integration using SSSD (recommended) or Winbind for seamless AD authentication

## Features

- ✅ Pre-integration validation and checks
- ✅ Windows domain join with optional OU placement
- ✅ Linux domain join using SSSD or Winbind
- ✅ Kerberos authentication setup
- ✅ DNS configuration and validation
- ✅ NTP/Time synchronization configuration
- ✅ SSH key-based authentication from AD
- ✅ Sudo access configuration for AD groups
- ✅ PAM and nsswitch configuration
- ✅ Home directory auto-creation for AD users
- ✅ Firewall rule configuration
- ✅ Audit logging and monitoring
- ✅ Configuration backup and rollback support
- ✅ Comprehensive verification and testing

## Requirements

### Hardware Requirements
- Network connectivity to AD server
- Static IP address recommended
- Sufficient disk space for SSSD cache

### Software Requirements

#### Windows
- Windows Server 2012 R2+ or Windows 7/8/10/11
- Administrator privileges
- Ansible for Windows support

#### Linux
- Ubuntu 16.04+, Debian 9+, CentOS 7+, RHEL 7+
- Python 3.x
- Standard package managers (apt, yum)
- sudo privileges

### Ansible Requirements
- Ansible 2.9 or later
- Windows: pywinrm, requests-kerberos
- Linux: pexpect (for interactive password input)

## Role Variables

### Required Variables

```yaml
ad_domain: "example.com"              # Your AD domain name
ad_domain_netbios: "EXAMPLE"          # NetBIOS domain name
ad_ad_server: "dc.example.com"        # FQDN or IP of AD server
ad_username: "administrator"          # AD user for domain join
ad_password: "{{ vault_password }}"   # AD user password (use vault!)
```

### DNS Configuration

```yaml
ad_dns_servers:
  - "192.168.1.10"  # Primary AD server IP
  - "192.168.1.11"  # Secondary AD server IP
ad_configure_dns: true
```

### NTP/Time Synchronization

```yaml
ad_configure_ntp: true
ad_ntp_servers:
  - "0.pool.ntp.org"
  - "1.pool.ntp.org"
```

### Windows Specific

```yaml
ad_windows_domain_join_ou: ""              # Optional OU path
ad_windows_restart_after_join: false       # Reboot after join
ad_windows_enable_gpo: true                # Apply Group Policy
```

### Linux SSSD Configuration

```yaml
ad_linux_ad_backend: "sssd"                # Use SSSD backend
ad_sssd_sudo_provider: "ldap"              # SSSD sudo provider
ad_linux_home_directory_base: "/home"      # Home directory location
ad_linux_shell_for_ad_users: "/bin/bash"   # Default shell
```

### Security Configuration

```yaml
ad_configure_sudoers: true
ad_local_admin_groups:
  - "Domain Admins"
  - "IT Admins"
ad_enforce_password_policy: true
```

### Backup and Rollback

```yaml
ad_backup_configuration: true
ad_backup_location: "/var/backups/ad-integration"
ad_enable_rollback: true
ad_rollback_on_failure: true
```

## Usage

### Basic Domain Join - Windows

```yaml
---
- hosts: windows_servers
  vars:
    ad_domain: "corp.example.com"
    ad_domain_netbios: "CORP"
    ad_ad_server: "dc1.corp.example.com"
    ad_username: "domain_joiner"
    ad_password: "{{ vault_domain_joiner_password }}"
    ad_dns_servers:
      - "192.168.1.10"
      - "192.168.1.11"
    
  roles:
    - ad-integration
```

### Advanced Configuration - Linux with SSSD

```yaml
---
- hosts: linux_servers
  vars:
    ad_domain: "corp.example.com"
    ad_domain_netbios: "CORP"
    ad_ad_server: "dc1.corp.example.com"
    ad_username: "ansible_joiner"
    ad_password: "{{ vault_ansible_password }}"
    
    # SSSD Configuration
    ad_linux_ad_backend: "sssd"
    ad_sssd_sudo_provider: "ldap"
    ad_linux_enable_home_directory_creation: true
    
    # Sudo configuration
    ad_configure_sudoers: true
    ad_local_admin_groups:
      - "Unix Admins"
      - "Linux Team"
    
    # SSH configuration
    ad_configure_ssh: true
    ad_ssh_pubkey_provider: "sssd"
    
    # NTP configuration
    ad_configure_ntp: true
    ad_ntp_servers:
      - "dc1.corp.example.com"
      - "0.pool.ntp.org"
    
    # DNS configuration
    ad_configure_dns: true
    ad_dns_servers:
      - "192.168.1.10"
      - "192.168.1.11"
    
    # Security
    ad_enable_audit_logging: true
    ad_update_hostname: true
    
  roles:
    - ad-integration
```

### With Custom OU Placement - Windows

```yaml
---
- hosts: windows_servers
  vars:
    ad_domain: "corp.example.com"
    ad_ad_server: "dc1.corp.example.com"
    ad_username: "joiner@corp.example.com"
    ad_password: "{{ vault_password }}"
    ad_windows_domain_join_ou: "OU=Servers,OU=Production,DC=corp,DC=example,DC=com"
    ad_windows_restart_after_join: true
    ad_windows_restart_timeout: 900
    
  roles:
    - ad-integration
```

## Task Files

### main.yml
Orchestrates the integration workflow:
1. Configuration backup
2. Pre-integration checks
3. OS-specific integration
4. Post-integration configuration
5. Integration verification

### pre_checks.yml
Validates:
- Required parameters
- Domain name format
- AD server reachability
- Network connectivity
- Current domain membership
- Credential validity

### windows.yml
Windows-specific integration:
1. DNS configuration
2. NTP/Time synchronization
3. Domain join
4. Group Policy update
5. Firewall rules
6. Password policy
7. Audit logging

### linux.yml
Linux-specific integration:
1. Package installation (SSSD/Winbind)
2. Kerberos configuration
3. SSSD/Samba configuration
4. PAM configuration
5. SSH configuration
6. Sudo configuration
7. Domain join
8. Service start/enable
9. Firewall configuration
10. DNS/NTP configuration

### post_integration.yml
Post-deployment configuration:
1. Cache clearing
2. Kerberos ticket initialization
3. Home directory auto-creation
4. Local group membership
5. Audit logging
6. Integration report generation

### verify_integration.yml
Comprehensive verification:
1. Domain membership validation
2. AD connectivity testing
3. User lookup verification
4. Group lookup verification
5. Kerberos authentication testing
6. Sudo access testing
7. SSH access validation

### backup_configuration.yml
Configuration backup:
1. Creates backup directory
2. Backs up critical configuration files
3. Captures system state
4. Generates backup inventory

## Templates

Configuration templates are provided for:
- **krb5.conf.j2** - Kerberos realm configuration
- **sssd.conf.j2** - SSSD domain configuration
- **smb.conf.j2** - Samba/Winbind configuration
- **nsswitch.conf.j2** - Name service switch configuration
- **sudoers.j2** - Sudo configuration for AD groups
- **resolv.conf.j2** - DNS resolver configuration
- **chrony.conf.j2** - NTP time synchronization
- **pam-sssd.j2** - PAM authentication configuration
- **autofs.j2** - Home directory automounting

## Security Considerations

1. **Credential Management**:
   - Always use `ansible-vault` for passwords
   - Use service accounts with minimal permissions
   - Never commit credentials to version control
   - Rotate domain join account passwords regularly

2. **Network Security**:
   - Use LDAPS (LDAP over SSL) for encrypted communication
   - Restrict AD replication traffic to authorized networks
   - Enable firewall rules before integration
   - Monitor AD authentication attempts

3. **Authentication Security**:
   - Enable Kerberos for strong authentication
   - Require strong passwords for AD accounts
   - Implement session timeouts
   - Disable basic authentication when possible

4. **Audit & Logging**:
   - Enable comprehensive audit logging
   - Monitor AD authentication events
   - Review security group memberships
   - Monitor failed login attempts

5. **Backup & Recovery**:
   - Always backup configuration before integration
   - Test rollback procedures
   - Store backups securely
   - Document recovery procedures

## Verification After Integration

### Windows Verification
```powershell
# Check domain membership
Get-WmiObject Win32_ComputerSystem | Select-Object Domain, PartOfDomain

# List AD users
Get-ADUser -Filter *

# Test Kerberos
klist

# Verify GPO application
gpupdate /force /report
```

### Linux Verification
```bash
# Check domain membership
realm list

# List AD users
getent passwd | grep sss

# Test Kerberos
kinit -k
klist

# Test LDAP
ldapwhoami -H ldap://dc.example.com

# Test sudo
sudo -l

# SSH with AD account
ssh aduser@localhost
```

## Troubleshooting

### Windows Issues

**Domain join fails**
- Verify AD server is reachable
- Check credentials
- Ensure DNS is configured correctly
- Check firewall rules

**Group Policy not applying**
```powershell
gpupdate /force
Get-WmiObject Win32_ComputerSystem | Get-ADDomain
```

### Linux Issues

**SSSD not starting**
```bash
systemctl status sssd
journalctl -xe -u sssd
sss_cache -E  # Clear cache
```

**User lookup fails**
```bash
getent passwd aduser
ldapwhoami -H ldap://dc.example.com

# Check SSSD cache
sss_cache -L
```

**Sudo not working**
```bash
sudo -l -U aduser
sudo -u aduser id

# Check sudoers
visudo -c -f /etc/sudoers.d/ad-integration
```

**SSH access issues**
```bash
sshd -t  # Test SSH config
ssh -vvv aduser@localhost  # Verbose SSH
```

## License

MIT

## Author

Your Organization

## Supported Platforms

### Windows
- Windows Server 2012 R2+
- Windows 7, 8, 8.1, 10, 11

### Linux
- Ubuntu 16.04+
- Debian 9+
- CentOS 7+
- RHEL 7+

## Example Playbooks

See the `examples/` directory for complete playbook examples.

## Contributing

Contributions are welcome! Please follow these guidelines:
1. Test changes thoroughly
2. Document modifications
3. Submit pull requests with clear descriptions

## Support

For issues or questions:
1. Check the troubleshooting section
2. Review Ansible logs for details
3. Consult AD and system logs
4. Contact your system administrator
