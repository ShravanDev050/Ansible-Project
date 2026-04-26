# Getting Started with Common Tools Infrastructure Collection

## Quick Start (5 minutes)

### 1. Install the Collection

```bash
ansible-galaxy collection install common_tools.infrastructure
```

### 2. Create a Simple Playbook

Create a file called `join-domain.yml`:

```yaml
---
- hosts: linux_servers
  become: yes
  collections:
    - common_tools.infrastructure
  
  vars:
    ad_domain: "corp.example.com"
    ad_ad_server: "dc.example.com"
    ad_username: "admin"
    ad_password: "password123"
    ad_linux_ad_backend: "sssd"
  
  roles:
    - ad-integration
```

### 3. Run the Playbook

```bash
ansible-playbook join-domain.yml -i inventory.ini
```

## Common Scenarios

### Scenario 1: Deploy New AD Domain

**Use the `ad` role to create a new Active Directory:**

```yaml
---
- hosts: ad_servers
  collections:
    - common_tools.infrastructure
  
  vars:
    ad_domain_name: "corp.example.com"
    ad_domain_netbios_name: "CORP"
    ad_admin_password: "SecurePassword123!"
  
  roles:
    - ad
```

**Requirements:**
- Windows Server 2012 R2+ or Linux system
- Administrator/root access
- At least 2GB RAM, 40GB disk

### Scenario 2: Join Windows Server to Domain

**Use the `ad-integration` role for Windows:**

```yaml
---
- hosts: windows_servers
  collections:
    - common_tools.infrastructure
  
  vars:
    ad_domain: "corp.example.com"
    ad_ad_server: "dc.example.com"
    ad_username: "domain_joiner"
    ad_password: "{{ vault_password }}"
  
  roles:
    - ad-integration
```

### Scenario 3: Join Linux Server to Domain

**Use the `ad-integration` role for Linux:**

```yaml
---
- hosts: linux_servers
  become: yes
  collections:
    - common_tools.infrastructure
  
  vars:
    ad_domain: "corp.example.com"
    ad_ad_server: "dc.example.com"
    ad_username: "admin"
    ad_password: "{{ vault_password }}"
    ad_linux_ad_backend: "sssd"
    ad_configure_sudo: yes
    ad_configure_ssh: yes
  
  roles:
    - ad-integration
```

## Setting Up Your Environment

### 1. Create Inventory File

Create `inventory.ini`:

```ini
[ad_servers]
dc1.corp.example.com ansible_host=192.168.1.10

[windows_servers]
ws1.corp.example.com ansible_host=192.168.1.20
ws2.corp.example.com ansible_host=192.168.1.21

[linux_servers]
ls1.corp.example.com ansible_host=192.168.1.30
ls2.corp.example.com ansible_host=192.168.1.31

[windows:vars]
ansible_connection=winrm
ansible_port=5985

[linux:vars]
ansible_connection=ssh
ansible_user=ubuntu
```

### 2. Create Vault File

Store sensitive data in vault:

```bash
ansible-vault create vars/vault.yml
```

Add to `vars/vault.yml`:

```yaml
vault_ad_admin_password: "SecurePassword123!"
vault_domain_joiner_password: "DomainJoinerPass123!"
vault_ansible_password: "AnsibleUserPass123!"
```

### 3. Create ansible.cfg

```ini
[defaults]
inventory = inventory.ini
vault_password_file = ~/.vault_pass
collections_paths = ~/.ansible/collections:/usr/share/ansible/collections

[inventory]
enable_plugins = ini, yaml
```

## Understanding the Roles

### AD Role (Installation)

**What it does:**
- Installs Active Directory on Windows
- Installs Samba AD on Linux
- Configures DNS, DHCP, NTP
- Sets up firewall rules
- Enables audit logging

**Key variables:**
```yaml
ad_domain_name: "example.com"
ad_domain_netbios_name: "EXAMPLE"
ad_forest_level: "2019"
ad_domain_level: "2019"
ad_admin_password: "password"
ad_install_dns: true
ad_install_dhcp: false
```

**When to use:**
- Setting up new AD infrastructure
- Creating new AD forests/domains
- Building Samba AD DC on Linux

### AD Integration Role (Domain Join)

**What it does:**
- Joins Windows to AD domain
- Joins Linux to AD domain (SSSD/Winbind)
- Configures DNS, DHCP, NTP
- Sets up Kerberos authentication
- Configures SSH and sudo
- Manages firewall rules

**Key variables:**
```yaml
ad_domain: "example.com"
ad_ad_server: "dc.example.com"
ad_username: "admin"
ad_password: "password"
ad_linux_ad_backend: "sssd"
ad_configure_ssh: true
ad_configure_sudoers: true
```

**When to use:**
- Joining existing servers to AD
- Integrating Linux systems
- Setting up domain authentication
- Configuring SSH/sudo access

## Troubleshooting

### Windows Domain Join Failed

**Check DNS:**
```powershell
nslookup dc.example.com
```

**Verify connectivity:**
```powershell
Test-NetConnection -ComputerName dc.example.com -Port 389
```

**Check credentials:**
```powershell
net user admin /domain
```

### Linux Domain Join Failed

**Check SSSD:**
```bash
systemctl status sssd
journalctl -u sssd -n 50
```

**Test LDAP:**
```bash
ldapwhoami -H ldap://dc.example.com
```

**Test Kerberos:**
```bash
kinit -k
klist
```

**Clear SSSD cache:**
```bash
sudo sss_cache -E
```

## Advanced Configuration

### Custom Home Directory Mount

```yaml
ad_configure_autofs: true
ad_autofs_home_mount: "/home"
ad_nfs_server: "nfs.example.com"
ad_nfs_export: "/exports/homes"
```

### Custom Sudoers Configuration

```yaml
ad_configure_sudoers: true
ad_sudo_groups:
  - "Unix Admins"
  - "Linux Operators"
ad_sudo_nopasswd_groups:
  - "Unix Admins"
```

### Multiple DNS Servers

```yaml
ad_dns_servers:
  - "192.168.1.10"
  - "192.168.1.11"
  - "8.8.8.8"
```

## Testing Your Configuration

### Test Domain User Lookup

**Windows:**
```powershell
whoami /groups
net user admin /domain
```

**Linux:**
```bash
id domain_user@example.com
getent passwd domain_user@example.com
getent group "domain_group@example.com"
```

### Test SSH Access

```bash
ssh -v domain_user@example.com@linux_server
```

### Test Kerberos

```bash
kinit -k
klist
```

## Next Steps

1. **Review the roles:**
   - [AD Role Documentation](./roles/ad/README.md)
   - [AD Integration Role Documentation](./roles/ad-integration/README.md)

2. **Look at examples:**
   - [Example Playbooks](./examples/)

3. **Check full documentation:**
   - [Collection README](./README.md)
   - [Installation Guide](./docs/INSTALLATION.md)

4. **Implement in your environment:**
   - Start with one role
   - Test in non-production first
   - Use vault for passwords
   - Monitor logs

## Getting Help

- Check [troubleshooting guide](./docs/TROUBLESHOOTING.md)
- Review role READMEs
- Check example playbooks
- Search GitHub issues
- Submit an issue with details

## Best Practices

1. **Always use ansible-vault for passwords**
   ```bash
   ansible-vault create vault.yml
   ```

2. **Test in non-production first**
   - Use development environment
   - Verify functionality
   - Check logs

3. **Use proper credentials**
   - Domain joiner account with minimal permissions
   - Separate credentials for Windows and Linux

4. **Enable backups**
   ```yaml
   ad_backup_before_join: true
   ad_backup_path: "/var/backups"
   ```

5. **Monitor and log**
   - Enable audit logging
   - Monitor SSSD/Winbind services
   - Review security logs

## Support

- [GitHub Repository](https://github.com/yourorg/common_tools.infrastructure)
- [Issue Tracker](https://github.com/yourorg/common_tools.infrastructure/issues)
- [Documentation](./README.md)
