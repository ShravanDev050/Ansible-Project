# AD Role

An Ansible role for installing and configuring Active Directory (AD) on Windows and Linux (Samba) systems.

## Overview

This role automates the deployment of Active Directory domain controllers on both Windows and Linux systems:
- **Windows**: Uses native Active Directory Domain Services (AD DS)
- **Linux**: Uses Samba as an Active Directory Compatible Domain Controller

## Features

- ✅ Pre-installation validation and checks
- ✅ Windows Active Directory forest and domain provisioning
- ✅ Linux Samba AD provisioning
- ✅ DNS configuration and management
- ✅ Optional DHCP installation and configuration
- ✅ Kerberos authentication setup
- ✅ NTP/Time synchronization configuration
- ✅ Firewall rule configuration
- ✅ SELinux/AppArmor compatibility
- ✅ Audit logging and monitoring setup
- ✅ Backup configuration
- ✅ Post-installation verification and testing

## Requirements

### Hardware Requirements
- **Disk Space**: Minimum 15 GB free
- **Memory**: Minimum 2 GB RAM (4+ GB recommended)
- **Network**: Static IP address recommended

### Software Requirements

#### Windows
- Windows Server 2012 R2 or later
- PowerShell 3.0 or later
- Ansible for Windows support

#### Linux
- Ubuntu 18.04+, Debian 9+, CentOS 7+, RHEL 7+
- Python 3.x
- Standard package managers (apt, yum)

### Ansible Requirements
- Ansible 2.9 or later
- `ansible-core` >= 2.9

## Role Variables

### Default Variables (defaults/main.yml)

#### Domain Configuration
```yaml
ad_domain_name: "example.com"
ad_domain_netbios_name: "EXAMPLE"
ad_forest_level: "2016"  # 2012, 2012R2, 2016, 2019, 2022
ad_domain_level: "2016"
```

#### Database Paths (Windows)
```yaml
ad_database_path: "C:\\Windows\\NTDS"
ad_log_path: "C:\\Windows\\NTDS"
ad_sysvol_path: "C:\\Windows\\SYSVOL"
```

#### LDAP/Kerberos Ports
```yaml
ad_ldap_port: 389
ad_ldaps_port: 636
ad_gc_port: 3268
ad_gc_ssl_port: 3269
```

#### Optional Services
```yaml
ad_install_dns: true
ad_install_dhcp: false
ad_dns_forwarders: []
```

#### Credentials
```yaml
ad_admin_user: "Administrator"
ad_admin_password: ""  # Use ansible-vault!
```

#### Linux/Samba Specific
```yaml
samba_realm: "EXAMPLE.COM"
samba_workgroup: "EXAMPLE"
samba_server_role: "active directory domain controller"
```

### Check Variables (vars/main.yml)

Pre-defined check variables including:
- OS detection
- Feature validation
- Port checking
- Password requirements
- Network connectivity checks

## Dependencies

None - this is a standalone role with no external role dependencies.

## Usage

### Basic Playbook

```yaml
---
- hosts: dc_servers
  vars:
    ad_domain_name: "corp.example.com"
    ad_domain_netbios_name: "CORP"
    ad_forest_level: "2016"
    ad_domain_level: "2016"
    ad_admin_password: "{{ vault_admin_password }}"
    ad_install_dns: true
    ad_install_dhcp: false
    
  roles:
    - ad
```

### With Custom Configuration

```yaml
---
- hosts: windows_dc
  vars:
    ad_domain_name: "corp.example.com"
    ad_domain_netbios_name: "CORP"
    ad_admin_password: "{{ vault_admin_password }}"
    ad_ntp_servers:
      - "0.pool.ntp.org"
      - "1.pool.ntp.org"
    ad_dhcp_scopes:
      - name: "Default Scope"
        start_ip: "192.168.1.100"
        end_ip: "192.168.1.200"
        subnet_mask: "255.255.255.0"
    ad_dns_forwarders:
      - "8.8.8.8"
      - "8.8.4.4"
    ad_backup_enabled: true
    ad_backup_location: "C:\\Backups\\AD"
    ad_backup_schedule: "weekly"
    
  roles:
    - ad
```

## Task Structure

The role is organized into multiple task files:

1. **main.yml** - Orchestrates the workflow
2. **pre_checks.yml** - Pre-installation validation
3. **windows.yml** - Windows-specific installation
4. **linux.yml** - Linux/Samba-specific installation
5. **post_installation.yml** - Post-deployment configuration

## Security Considerations

1. **Password Management**:
   - Always use `ansible-vault` for sensitive passwords
   - Never commit unencrypted passwords to version control
   - Enforce strong password policies

2. **Network Security**:
   - Restrict AD replication traffic to authorized networks
   - Configure firewall rules appropriately
   - Use LDAPS for encrypted LDAP communication

3. **Audit Logging**:
   - Enable comprehensive audit logging
   - Monitor account and directory service access
   - Regularly review AD audit logs

4. **Backup Strategy**:
   - Enable automated backups
   - Store backups securely
   - Test restore procedures regularly

## License

MIT

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
