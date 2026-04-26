# Notepad++ Installation and Configuration Role

An Ansible role for installing and configuring Notepad++ text editor on Windows servers.

## Role Information

- **Name**: notepadplusplus
- **Namespace**: common_tools
- **Collection**: infrastructure
- **Version**: 1.0.0
- **License**: MIT

## Overview

This role provides comprehensive automation for:

- Installing Notepad++ on Windows systems
- Configuring installation paths and shortcuts
- Setting up file associations
- Enabling backup and session management
- Creating desktop and Start Menu shortcuts
- Verifying installation integrity
- Managing multiple installation methods

## Supported Platforms

### Windows Versions
- Windows 7
- Windows 8, 8.1
- Windows 10, 11
- Windows Server 2012 R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2022

### System Requirements
- Administrator privileges
- Minimum 50 MB disk space
- .NET Framework 4.5+ (for some features)
- Internet connectivity (for download-based installations)

## Installation Methods

The role supports multiple installation methods:

1. **Chocolatey** (Default) - Package manager installation
2. **MSI** - Direct MSI installer download and installation
3. **Portable** - Portable version extraction
4. **ZIP** - ZIP archive extraction

## Role Variables

### Basic Configuration

```yaml
# Installation method (default: 'chocolatey')
notepadplusplus_install_method: "chocolatey"

# Version to install (default: 'latest')
notepadplusplus_version: "latest"

# Installation directory
notepadplusplus_install_path: "C:\\Program Files\\Notepad++"

# Create Start Menu shortcut
notepadplusplus_create_shortcut: true

# Create Desktop shortcut
notepadplusplus_create_desktop_shortcut: false

# Add to PATH environment variable
notepadplusplus_add_to_path: false
```

### Advanced Configuration

```yaml
# File association configuration
notepadplusplus_configure_file_associations: false
notepadplusplus_file_associations:
  - ".txt"
  - ".log"
  - ".conf"
  - ".yml"
  - ".json"

# Backup and restoration
notepadplusplus_backup_config: true
notepadplusplus_backup_before_join: true
notepadplusplus_backup_dir: "C:\\Backups\\Notepad++"

# Auto-update and session management
notepadplusplus_enable_auto_update: true
notepadplusplus_enable_session_backup: true
notepadplusplus_session_backup_interval: 600

# Font and theme
notepadplusplus_font_name: "Consolas"
notepadplusplus_font_size: 10
notepadplusplus_theme: "default"

# Logging
notepadplusplus_enable_logging: true
notepadplusplus_log_dir: "C:\\Logs\\Notepad++"

# Installation behavior
notepadplusplus_force_install: false
notepadplusplus_uninstall_before_install: false
```

### Download Configuration

```yaml
# Download retry settings
notepadplusplus_download_retries: 3
notepadplusplus_download_retry_delay: 5
notepadplusplus_download_timeout: 300  # seconds

# Download directory
notepadplusplus_download_dir: "C:\\Windows\\Temp"
```

## Default Variables

All default variables are defined in `defaults/main.yml`. Review this file for the complete list of configurable options.

## Task Files

- `main.yml` - Main orchestration and workflow
- `pre_checks.yml` - Pre-installation validation
- `pre_install.yml` - Configuration backup before installation
- `install.yml` - Installation tasks for all methods
- `configure.yml` - Post-installation configuration
- `verify.yml` - Installation verification

## Usage Examples

### Basic Installation with Default Settings

```yaml
---
- hosts: windows_servers
  collections:
    - common_tools.infrastructure
  
  roles:
    - notepadplusplus
```

### Install Specific Version

```yaml
---
- hosts: windows_servers
  collections:
    - common_tools.infrastructure
  
  vars:
    notepadplusplus_version: "8.4.6"
    notepadplusplus_install_method: "msi"
  
  roles:
    - notepadplusplus
```

### Install with File Associations and Desktop Shortcut

```yaml
---
- hosts: windows_servers
  collections:
    - common_tools.infrastructure
  
  vars:
    notepadplusplus_create_desktop_shortcut: true
    notepadplusplus_configure_file_associations: true
    notepadplusplus_file_associations:
      - ".txt"
      - ".log"
      - ".conf"
      - ".yml"
      - ".json"
      - ".py"
      - ".sh"
  
  roles:
    - notepadplusplus
```

### Install via Chocolatey with Auto-update Disabled

```yaml
---
- hosts: windows_servers
  collections:
    - common_tools.infrastructure
  
  vars:
    notepadplusplus_install_method: "chocolatey"
    notepadplusplus_enable_auto_update: false
    notepadplusplus_add_to_path: true
  
  roles:
    - notepadplusplus
```

### Force Reinstall Latest Version

```yaml
---
- hosts: windows_servers
  collections:
    - common_tools.infrastructure
  
  vars:
    notepadplusplus_force_install: true
    notepadplusplus_version: "latest"
    notepadplusplus_uninstall_before_install: true
  
  roles:
    - notepadplusplus
```

### Complete Configuration

```yaml
---
- hosts: windows_servers
  collections:
    - common_tools.infrastructure
  
  vars:
    # Installation
    notepadplusplus_install_method: "msi"
    notepadplusplus_version: "8.5.1"
    notepadplusplus_install_path: "C:\\Program Files\\Notepad++"
    
    # Shortcuts
    notepadplusplus_create_shortcut: true
    notepadplusplus_create_desktop_shortcut: true
    notepadplusplus_add_to_path: true
    
    # File associations
    notepadplusplus_configure_file_associations: true
    
    # Configuration
    notepadplusplus_font_name: "Consolas"
    notepadplusplus_font_size: 11
    notepadplusplus_enable_auto_update: true
    notepadplusplus_enable_session_backup: true
    
    # Backup
    notepadplusplus_backup_config: true
    notepadplusplus_backup_dir: "C:\\Backups\\Notepad++"
    
    # Logging
    notepadplusplus_enable_logging: true
    notepadplusplus_log_dir: "C:\\Logs\\Notepad++"
    
    # Verification
    notepadplusplus_verify_installation: true
  
  roles:
    - notepadplusplus
```

## Pre-Installation Checks

The role performs the following validation checks:

- ✓ Installation method validation
- ✓ Windows version compatibility
- ✓ Administrator privilege verification
- ✓ Available disk space validation
- ✓ Internet connectivity check (for download methods)
- ✓ Chocolatey installation verification (for Chocolatey method)
- ✓ Font size validation
- ✓ Installation path validation
- ✓ Download retry configuration validation

## Installation Workflow

1. **Pre-checks** - Validate system requirements and configuration
2. **Pre-install** - Backup existing configuration if present
3. **Installation** - Install Notepad++ using selected method
4. **Configuration** - Configure shortcuts, paths, and settings
5. **Verification** - Verify installation success and functionality
6. **Reporting** - Generate installation report and logs

## Backup and Recovery

The role supports configuration backup before installation:

```yaml
notepadplusplus_backup_config: true
notepadplusplus_config_backup_dir: "C:\\Backups\\Notepad++"
```

Backups are created with timestamp in filename:
- Format: `notepadplusplus-config-backup-YYYYMMDD-HHMMSS.zip`

## File Associations

To associate file types with Notepad++:

```yaml
notepadplusplus_configure_file_associations: true
notepadplusplus_file_associations:
  - ".txt"
  - ".log"
  - ".conf"
  - ".yml"
  - ".json"
  - ".xml"
  - ".py"
  - ".js"
```

## Logging and Reporting

Installation logs are created when enabled:

```yaml
notepadplusplus_enable_logging: true
notepadplusplus_log_dir: "C:\\Logs\\Notepad++"
```

Logs include:
- Installation start/completion times
- Version information
- Configuration details
- Verification results
- Any errors encountered

## Error Handling

The role includes comprehensive error handling:

- Installation retries with configurable delays
- Detailed error messages
- Installation failure logging
- Graceful failure with informative messages

## Verification

Post-installation verification checks:

- ✓ Executable existence
- ✓ File version retrieval
- ✓ Process launch test
- ✓ PATH environment variable
- ✓ Shortcut creation
- ✓ Configuration directory
- ✓ Registry installation record

## Performance Considerations

- Download timeout: 300 seconds (configurable)
- Installation timeout: 10 minutes (configurable)
- Session backup interval: 600 seconds

## Security Considerations

- Administrator privileges required
- TLS 1.2 for downloads
- Secure backup storage recommended
- Regular backup retention policy

## Troubleshooting

### Installation Fails with "Not Found"

Check:
1. Internet connectivity (for download methods)
2. Disk space availability
3. Administrator privileges
4. Windows version compatibility

### Chocolatey Method Fails

Ensure Chocolatey is installed:
```powershell
choco --version
```

Or use alternative installation method:
```yaml
notepadplusplus_install_method: "msi"
```

### Shortcut Not Created

Verify:
1. Notepad++ installation path exists
2. User has write permissions to shortcuts location
3. Notepad++ executable exists at expected path

### File Associations Not Working

File associations may require:
1. User profile reload
2. Registry permissions
3. Windows file explorer cache clear

## Rollback Procedure

To rollback to previous installation:

```powershell
# Remove current version
Remove-Item "C:\Program Files\Notepad++" -Recurse -Force

# Restore from backup
Expand-Archive "C:\Backups\Notepad++\notepadplusplus-config-backup-*.zip" `
  -DestinationPath "$env:APPDATA\Notepad++"
```

## Compatibility

- Ansible: 2.9+
- Python: 3.6+
- Windows: 7 and newer (including Server editions)
- PowerShell: 3.0+

## Dependencies

None - This role is standalone with no external role dependencies.

## License

This role is licensed under the MIT License.

## Author

Your Organization

## Support

For issues or feature requests, refer to the collection documentation or submit an issue on GitHub.
