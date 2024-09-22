# Ansible Role: JetBrains Toolbox

## Role Name

`jetbrains-toolbox`

This Ansible role installs or uninstalls JetBrains Toolbox, a tool for managing JetBrains IDEs, on Linux machines.

## Requirements

- Ansible version: 2.9 or higher.
- Root privileges (using become: yes), as installation happens in system directories such as /opt/jetbrains-toolbox and symlinks are created in /usr/local/bin.
- Internet access on the managed machine to download JetBrains Toolbox from JetBrains' website.

### Role Variables

#### Default Variables

The following variables can be customized to control the behavior of the role:

|        Variable        | Description                                            | Default Value                                                                              |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------ |
| `toolbox_action`       | Defines whether to install or uninstall Toolbox.       | `install`                                                                                  |
| `toolbox_version`      | The version of Toolbox to install (currently ignored). | `latest`                                                                                   |
| `toolbox_install_dir`  | Directory where JetBrains Toolbox will be installed.   | `/opt/jetbrains-toolbox`                                                                   |
| `toolbox_symlink_path` | Path for creating a symlink to the jetbrains-toolbox.  | `/usr/local/bin/jetbrains-toolbox`                                                         |
| `toolbox_download_url` | The URL to download the Toolbox tarball.               | <https://data.services.jetbrains.com/products/download?code=TBA&latest&distribution=linux> |

#### Action Control

- `toolbox_action`: Determines whether to install or uninstall JetBrains Toolbox. Valid values:
- `install`: Installs the latest version of JetBrains Toolbox.
- `uninstall`: Uninstalls JetBrains Toolbox and removes the associated symlink and installation directory.

### Dependencies

None.

### Example Playbook

#### Installing JetBrains Toolbox

```yaml
---
- hosts: dev-machines
  become: yes
  roles:
    - role: jetbrains_toolbox
      vars:
        toolbox_action: "install"
```

#### Uninstalling JetBrains Toolbox

```yaml
---
- hosts: dev-machines
  become: yes
  roles:
    - role: jetbrains_toolbox
      vars:
        toolbox_action: "uninstall"
```

### Running the Playbook

To install JetBrains Toolbox, run:

```bash
ansible-playbook -i inventory playbook.yml --extra-vars "toolbox_action=install"
```

To uninstall JetBrains Toolbox, run:

```bash
ansible-playbook -i inventory playbook.yml --extra-vars "toolbox_action=uninstall"
```

## License

MIT

## Author Information

This role was created at 2024 by [fmarcelinoPT](https://github.com/fmarcelinoPT). Feel free to customize or extend the role to fit your needs.

## Usage

- Install: This playbook will download the latest version of JetBrains Toolbox, extract it to the specified directory, create a symlink, and create a desktop shortcut.
- Uninstall: This playbook will remove the installed JetBrains Toolbox directory, symlink, and desktop shortcut.
