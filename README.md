# ansible-role-jetbrains-toolbox

Ansible Role: JetBrains Toolbox Installer and Uninstaller
Below is the complete Ansible role to install and uninstall JetBrains Toolbox on a Linux machine, including the README.md in markdown format.

Role Structure
css
Copy code
roles/
└── jetbrains_toolbox/
    ├── defaults/
    │   └── main.yml
    ├── tasks/
    │   ├── main.yml
    │   ├── install.yml
    │   └── revert.yml
    ├── templates/
    │   └── toolbox.desktop.j2
    ├── handlers/
    │   └── main.yml
    ├── vars/
    │   └── main.yml
    └── meta/
        └── main.yml
defaults/main.yml
This file contains the default variables that will be used in the role.

yaml
Copy code
toolbox_action: "install"  # Default action is 'install', can be 'uninstall'
toolbox_version: "latest"
toolbox_install_dir: "/opt/jetbrains-toolbox"
toolbox_symlink_path: "/usr/local/bin/jetbrains-toolbox"
toolbox_download_url: "https://data.services.jetbrains.com/products/download?code=TBA&latest&distribution=linux"
tasks/main.yml
The main.yml file will dynamically load either the installation or uninstallation tasks based on the value of toolbox_action.

yaml
Copy code
---
- name: Install or Revert JetBrains Toolbox based on user action
  include_tasks: "{{ toolbox_action }}.yml"
tasks/install.yml
This file contains the tasks for installing JetBrains Toolbox.

yaml
Copy code
---
# Installation Tasks
- name: Ensure the installation directory exists
  file:
    path: "{{ toolbox_install_dir }}"
    state: directory
    mode: '0755'

- name: Download JetBrains Toolbox
  get_url:
    url: "{{ toolbox_download_url }}"
    dest: "/tmp/jetbrains_toolbox.tar.gz"
    mode: "0644"

- name: Extract JetBrains Toolbox
  become: true
  unarchive:
    src: "/tmp/jetbrains_toolbox.tar.gz"
    dest: "{{ toolbox_install_dir }}"
    remote_src: true

- name: Create symlink for Toolbox
  file:
    src: "{{ toolbox_install_dir }}/jetbrains-toolbox"
    dest: "{{ toolbox_symlink_path }}"
    state: link

# Create a desktop shortcut for Toolbox
- name: Create Toolbox desktop shortcut
  template:
    src: "toolbox.desktop.j2"
    dest: "{{ ansible_env.HOME }}/.local/share/applications/toolbox.desktop"
    mode: '0644'
tasks/revert.yml
This file contains the tasks for uninstalling JetBrains Toolbox.

yaml
Copy code
---
# Revert (Uninstall) Tasks
- name: Remove the JetBrains Toolbox installation directory
  file:
    path: "{{ toolbox_install_dir }}"
    state: absent

- name: Remove the symlink to jetbrains-toolbox
  file:
    path: "{{ toolbox_symlink_path }}"
    state: absent

# Remove the Toolbox desktop shortcut
- name: Remove Toolbox desktop shortcut
  file:
    path: "{{ ansible_env.HOME }}/.local/share/applications/toolbox.desktop"
    state: absent
templates/toolbox.desktop.j2
This is the Jinja2 template for creating a desktop shortcut for JetBrains Toolbox.

ini
Copy code
[Desktop Entry]
Version=1.0
Type=Application
Name=JetBrains Toolbox
Exec={{ toolbox_install_dir }}/jetbrains-toolbox
Icon={{ toolbox_install_dir }}/jetbrains-toolbox.svg
Terminal=false
Categories=Development;
README.md
Here is the README.md file in markdown format.

Ansible Role: JetBrains Toolbox
Role Name
jetbrains_toolbox

This Ansible role installs or uninstalls JetBrains Toolbox, a tool for managing JetBrains IDEs, on Linux machines.

Requirements
Ansible version: 2.9 or higher.
Root privileges (using become: yes), as installation happens in system directories such as /opt/jetbrains-toolbox and symlinks are created in /usr/local/bin.
Internet access on the managed machine to download JetBrains Toolbox from JetBrains' website.
Role Variables
Default Variables
The following variables can be customized to control the behavior of the role:

Variable	Description	Default Value
toolbox_action	Defines whether to install or uninstall Toolbox.	install
toolbox_version	The version of Toolbox to install (currently ignored).	latest
toolbox_install_dir	Directory where JetBrains Toolbox will be installed.	/opt/jetbrains-toolbox
toolbox_symlink_path	Path for creating a symlink to the jetbrains-toolbox.	/usr/local/bin/jetbrains-toolbox
toolbox_download_url	The URL to download the Toolbox tarball.	https://data.services.jetbrains.com/products/download?code=TBA&latest&distribution=linux
Action Control
toolbox_action: Determines whether to install or uninstall JetBrains Toolbox. Valid values:
install: Installs the latest version of JetBrains Toolbox.
uninstall: Uninstalls JetBrains Toolbox and removes the associated symlink and installation directory.
Dependencies
None.

Example Playbook
Installing JetBrains Toolbox
yaml
Copy code
---
- hosts: dev-machines
  become: yes
  roles:
    - role: jetbrains_toolbox
      vars:
        toolbox_action: "install"
Uninstalling JetBrains Toolbox
yaml
Copy code
---
- hosts: dev-machines
  become: yes
  roles:
    - role: jetbrains_toolbox
      vars:
        toolbox_action: "uninstall"
Running the Playbook
To install JetBrains Toolbox, run:

bash
Copy code
ansible-playbook -i inventory playbook.yml --extra-vars "toolbox_action=install"
To uninstall JetBrains Toolbox, run:

bash
Copy code
ansible-playbook -i inventory playbook.yml --extra-vars "toolbox_action=uninstall"
License
MIT

Author Information
This role was created by [Your Name]. Feel free to customize or extend the role to fit your needs.

Usage:
Install: This playbook will download the latest version of JetBrains Toolbox, extract it to the specified directory, create a symlink, and create a desktop shortcut.
Uninstall: This playbook will remove the installed JetBrains Toolbox directory, symlink, and desktop shortcut.
Let me know if you need more details or customization!
