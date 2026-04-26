# Common Tools Infrastructure Collection

An Ansible collection for managing Active Directory infrastructure across Windows and Linux environments.

**Namespace:** `common_tools`  
**Collection:** `infrastructure`  
**Version:** 1.0.0

## Overview

The `common_tools.infrastructure` collection provides comprehensive Ansible roles for:

1. **Installing and configuring Active Directory** - Create new AD forests and domains
2. **Integrating systems into existing Active Directory** - Join Windows and Linux systems to AD

## What's Included

### Roles

This collection includes two complementary roles:

#### 1. `ad` - Active Directory Installation
Complete role for deploying Active Directory domain controllers on Windows and Linux (Samba).

**Supported Platforms:**
- Windows Server 2012 R2 through 2022
- Ubuntu 18.04+
- Debian 9+
- CentOS 7+
- RHEL 7+

**Key Features:**
- AD forest and domain provisioning
- Windows feature installation and configuration
- Linux/Samba AD DC setup
- DNS and DHCP configuration
- Kerberos realm configuration
- Time synchronization setup
- Firewall and audit logging configuration
- Automated backup support

**Use Case:** Create a new Active Directory infrastructure from scratch

#### 2. `ad-integration` - Active Directory Integration
Complete role for joining Windows and Linux systems to existing Active Directory domains.

**Supported Platforms:**
- Windows Server 2012 R2 through 2022
- Windows 7, 8, 8.1, 10, 11
- Ubuntu 16.04+
- Debian 9+
- CentOS 7+
- RHEL 7+

**Key Features:**
- Windows domain join with custom OU placement
- Linux domain join using SSSD or Winbind
- SSH key-based authentication from AD
- Sudo access for AD groups
- Home directory auto-creation
- Kerberos authentication setup
- DNS and NTP configuration
- Pre-integration backup with rollback support
- Comprehensive verification testing

**Use Case:** Integrate existing servers into an Active Directory domain

## Installation

### Install from Galaxy

```bash
ansible-galaxy collection install common_tools.infrastructure
```

### Install from Git Repository

```bash
ansible-galaxy collection install git+https://github.com/yourorg/common_tools.infrastructure.git
```

### Manual Installation

```bash
git clone https://github.com/yourorg/common_tools.infrastructure.git
mkdir -p ~/.ansible/collections/ansible_collections/common_tools
cp -r common_tools.infrastructure ~/.ansible/collections/ansible_collections/common_tools/infrastructure
```

## Quick Start

### Deploy a New AD Domain (Windows)

```yaml
---
- hosts: windows_servers
  collections:
    - common_tools.infrastructure
  
  vars:
    ad_domain_name: "corp.example.com"
    ad_domain_netbios_name: "CORP"
    ad_forest_level: "2019"
    ad_domain_level: "2019"
    ad_admin_password: "{{ vault_ad_admin_password }}"
    ad_install_dns: true
    ad_install_dhcp: true
  
  roles:
    - ad
```

### Deploy Samba AD DC (Linux)

```yaml
---
- hosts: linux_dc
  collections:
    - common_tools.infrastructure
  
  vars:
    ad_domain_name: "corp.example.com"
    ad_domain_netbios_name: "CORP"
    ad_admin_password: "{{ vault_ad_admin_password }}"
  
  roles:
    - ad
```

### Join Windows Server to AD Domain

```yaml
---
- hosts: windows_servers
  collections:
    - common_tools.infrastructure
  
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

### Join Linux Server to AD Domain (SSSD)

```yaml
---
- hosts: linux_servers
  collections:
    - common_tools.infrastructure
  
  vars:
    ad_domain: "corp.example.com"
    ad_domain_netbios: "CORP"
    ad_ad_server: "dc1.corp.example.com"
    ad_username: "ansible_joiner"
    ad_password: "{{ vault_ansible_password }}"
    ad_linux_ad_backend: "sssd"
    ad_configure_sudoers: true
    ad_local_admin_groups:
      - "Unix Admins"
    ad_configure_ssh: true
  
  roles:
    - ad-integration
```

## Role Usage Documentation

### Role: `ad`

Installs and configures Active Directory domain controllers.

**See also:** [AD Role Documentation](./roles/ad/README.md)

**Key Variables:**
```yaml
ad_domain_name: "example.com"
ad_domain_netbios_name: "EXAMPLE"
ad_forest_level: "2019"
ad_domain_level: "2019"
ad_admin_password: "SecurePassword123!"
ad_install_dns: true
ad_install_dhcp: false
```

**Task Files:**
- `main.yml` - Orchestration
- `pre_checks.yml` - Pre-installation validation
- `windows.yml` - Windows AD installation
- `linux.yml` - Linux/Samba AD installation
- `post_installation.yml` - Post-deployment configuration

### Role: `ad-integration`

Integrates existing systems into Active Directory domains.

**See also:** [AD Integration Role Documentation](./roles/ad-integration/README.md)

**Key Variables:**
```yaml
ad_domain: "example.com"
ad_ad_server: "dc.example.com"
ad_username: "administrator"
ad_password: "{{ vault_password }}"
ad_linux_ad_backend: "sssd"  # or "winbind"
ad_configure_sudoers: true
ad_configure_ssh: true
```

**Task Files:**
- `main.yml` - Orchestration
- `pre_checks.yml` - Pre-integration validation
- `windows.yml` - Windows domain join
- `linux.yml` - Linux domain join
- `post_integration.yml` - Post-integration configuration
- `verify_integration.yml` - Integration verification

## Requirements

### Ansible Requirements
- Ansible 2.9 or later
- ansible-core >= 2.9

### Python Requirements
- Python 3.6+

### Windows Requirements
- Windows PowerShell 3.0+
- WinRM enabled for Ansible
- Administrator privileges

### Linux Requirements
- sudo privileges
- Standard package managers (apt, yum, dnf)
- Python 3.x

## Security Best Practices

1. **Password Management**
   - Always use `ansible-vault` for sensitive data
   - Use strong, unique passwords
   - Rotate credentials regularly

2. **Network Security**
   - Restrict AD traffic to authorized networks
   - Use LDAPS (LDAP over SSL) when possible
   - Enable firewall rules appropriately

3. **Access Control**
   - Use service accounts with minimal permissions
   - Implement principle of least privilege
   - Regular audit of AD permissions

4. **Backup & Recovery**
   - Enable automatic backups
   - Test recovery procedures
   - Store backups securely

5. **Logging & Monitoring**
   - Enable comprehensive audit logging
   - Monitor authentication attempts
   - Review logs regularly

## Examples

Complete example playbooks are available in the `examples/` directory:

- `deploy_ad_forest_windows.yml` - Deploy new AD forest on Windows
- `deploy_ad_dc_samba_linux.yml` - Deploy Samba AD DC on Linux
- `join_domain_windows_servers.yml` - Join Windows servers to domain
- `join_domain_linux_servers.yml` - Join Linux servers to domain
- `integrate_mixed_environment.yml` - Integrate both Windows and Linux

## Troubleshooting

### Windows Domain Join Failures
```powershell
# Check domain status
Get-WmiObject Win32_ComputerSystem | Select-Object Domain, PartOfDomain

# Verify DNS resolution
nslookup example.com

# Check network connectivity
Test-NetConnection -ComputerName dc.example.com -Port 389
```

### Linux Domain Join Failures
```bash
# Check SSSD status
systemctl status sssd
journalctl -u sssd -n 50

# Verify LDAP connectivity
ldapwhoami -H ldap://dc.example.com

# Test Kerberos
kinit -k

# Clear SSSD cache
sss_cache -E
```

### Common Issues & Solutions

**DNS Resolution Issues**
- Verify AD server DNS is configured
- Check `/etc/resolv.conf` (Linux) or DNS settings (Windows)
- Test with `nslookup` or `ldapwhoami`

**Firewall Blocking**
- Ensure required ports are open (53, 88, 389, 636, 3268)
- Check local firewall rules
- Verify AD server firewall rules

**Credential Issues**
- Verify username/password are correct
- Ensure domain_joiner account has permissions
- Check account is not locked

**Time Synchronization**
- AD requires time sync within 5 minutes
- Configure NTP servers
- Use `ntpdate` or `chrony` to sync

## Collection Contents

```
common_tools/
├── infrastructure/
│   ├── galaxy.yml              # Collection metadata
│   ├── README.md               # This file
│   ├── MANIFEST.json           # Collection manifest
│   ├── docs/                   # Collection documentation
│   ├── roles/
│   │   ├── ad/                 # AD installation role
│   │   └── ad-integration/     # AD integration role
│   ├── plugins/                # Ansible plugins
│   └── examples/               # Example playbooks
```

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## Versioning

This collection follows [Semantic Versioning](https://semver.org/):
- MAJOR version for incompatible API changes
- MINOR version for new functionality (backwards compatible)
- PATCH version for bug fixes

## License

This collection is licensed under the MIT License. See LICENSE file for details.

## Support

For issues, feature requests, or questions:

1. Check existing documentation
2. Review troubleshooting section
3. Search existing issues
4. Submit an issue with detailed information

## Related Documentation

- [Ansible Collections Documentation](https://docs.ansible.com/ansible/latest/collections/)
- [Active Directory Administration](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/get-started-with-active-directory-domain-services-ad-ds-installation-and-removal-level-100)
- [Samba Active Directory](https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller)

## Changelog

### Version 1.0.0
- Initial release
- Two roles: `ad` and `ad-integration`
- Support for Windows Server 2012 R2 through 2022
- Support for Ubuntu, Debian, CentOS, RHEL
- Comprehensive documentation and examples
