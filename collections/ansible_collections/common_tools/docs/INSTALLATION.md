# Common Tools Infrastructure Collection - Installation Guide

## Overview

The `common_tools.infrastructure` collection provides Ansible roles for Active Directory installation and integration.

## Installation Methods

### 1. From Ansible Galaxy

The recommended way to install the collection:

```bash
ansible-galaxy collection install common_tools.infrastructure
```

The collection will be installed to:
```
~/.ansible/collections/ansible_collections/common_tools/infrastructure/
```

Or specify a custom path:
```bash
ansible-galaxy collection install common_tools.infrastructure -p /path/to/collections
```

### 2. From GitHub Repository

Install directly from Git:

```bash
ansible-galaxy collection install git+https://github.com/yourorg/common_tools.infrastructure.git
```

### 3. Manual Installation

Clone and install manually:

```bash
# Clone repository
git clone https://github.com/yourorg/common_tools.infrastructure.git
cd common_tools.infrastructure

# Create collection directory
mkdir -p ~/.ansible/collections/ansible_collections/common_tools

# Copy collection
cp -r . ~/.ansible/collections/ansible_collections/common_tools/infrastructure
```

### 4. From Source Archive

```bash
# Download archive
wget https://github.com/yourorg/common_tools.infrastructure/archive/refs/tags/v1.0.0.tar.gz

# Extract
tar -xzf v1.0.0.tar.gz

# Install
ansible-galaxy collection install /path/to/extracted/archive
```

## Verify Installation

After installation, verify the collection is available:

```bash
ansible-galaxy collection list | grep common_tools
```

Or test in a playbook:

```yaml
---
- hosts: localhost
  gather_facts: false
  collections:
    - common_tools.infrastructure
  
  tasks:
    - name: Test collection import
      debug:
        msg: "Common Tools Infrastructure collection is installed"
```

## Configuration

### Ansible Configuration File

Add the collection to your `ansible.cfg` if needed:

```ini
[defaults]
collections_paths = ~/.ansible/collections:/usr/share/ansible/collections

[inventory]
enable_plugins = ini, yaml, aws_ec2
```

### Collections Paths

The following paths are searched for collections in order:

1. `COLLECTIONS_PATHS` environment variable
2. `collections_paths` in `ansible.cfg`
3. `~/.ansible/collections`
4. `/usr/share/ansible/collections`

## Requirements

### Ansible
- Ansible 2.9 or later
- ansible-core >= 2.9

### Python
- Python 3.6+

### Windows (for AD role)
- PowerShell 3.0+
- WinRM configured

### Linux
- sudo/root access
- Standard package managers

### Credentials
- AD administrator account
- Domain joiner account (for integration)

## Using the Collection in Playbooks

Reference the collection explicitly:

```yaml
---
- hosts: all
  collections:
    - common_tools.infrastructure
  
  roles:
    - ad
```

Or use the fully qualified collection name:

```yaml
---
- hosts: all
  
  roles:
    - common_tools.infrastructure.ad
```

## Updating the Collection

Update to the latest version:

```bash
ansible-galaxy collection install common_tools.infrastructure --upgrade
```

Or upgrade all collections:

```bash
ansible-galaxy collection install -r requirements.yml --upgrade
```

## Using with requirements.yml

Create a `requirements.yml` file:

```yaml
---
collections:
  - name: common_tools.infrastructure
    version: ">=1.0.0"
  - name: community.general
  - name: ansible.windows
```

Install all requirements:

```bash
ansible-galaxy install -r requirements.yml
```

## Troubleshooting

### Collection Not Found

If you get "collection not found" errors:

1. Verify installation:
   ```bash
   ansible-galaxy collection list
   ```

2. Check COLLECTIONS_PATHS:
   ```bash
   ansible-config dump | grep COLLECTIONS_PATHS
   ```

3. Reinstall collection:
   ```bash
   ansible-galaxy collection install common_tools.infrastructure --force
   ```

### Permission Denied

If you get permission errors during installation:

```bash
# Install to user directory
ansible-galaxy collection install common_tools.infrastructure -p ~/.ansible/collections

# Or use sudo for system-wide installation
sudo ansible-galaxy collection install common_tools.infrastructure
```

### Python Dependency Issues

Some modules may require additional Python packages:

```bash
# Install Ansible requirements
pip install ansible

# Install Windows support
pip install pywinrm requests-kerberos

# Install SSSD/Winbind support on Linux
pip install pexpect
```

## Development Installation

For development or contribution:

```bash
# Clone the repository
git clone https://github.com/yourorg/common_tools.infrastructure.git
cd common_tools.infrastructure

# Create symlink for development
mkdir -p ~/.ansible/collections/ansible_collections/common_tools
ln -s $(pwd) ~/.ansible/collections/ansible_collections/common_tools/infrastructure
```

## Uninstall

To remove the collection:

```bash
rm -rf ~/.ansible/collections/ansible_collections/common_tools
```

Or use ansible-galaxy:

```bash
ansible-galaxy collection uninstall common_tools.infrastructure
```

## Next Steps

1. Review [README.md](./README.md) for collection overview
2. Check role documentation:
   - [AD Installation Role](./roles/ad/README.md)
   - [AD Integration Role](./roles/ad-integration/README.md)
3. Review example playbooks in `examples/` directory
4. Test with your environment

## Support

For issues or questions:
- Check existing [documentation](./docs/)
- Review [troubleshooting](./docs/TROUBLESHOOTING.md)
- Submit an [issue](https://github.com/yourorg/common_tools.infrastructure/issues)
