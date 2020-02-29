# Ansible - Manjaro Linux Workstation
Ansible playbook for setting up a Manjaro workstation, and installing and configuring desired applications.

## Installing Ansible
Use one of Manjaro's package managers to install Ansible: `sudo pacman -S ansible` or `yay -S ansible`. Alternatively, Python can  be used to intall Ansible with a pip command: `pip install --user ansible`. Note that the flag `--user` is needed when using pip to install packages on Manjaro to prevent conflicts with pacman.

Optional: Install [Ansible Lint](https://docs.ansible.com/ansible-lint/), which analyzes code for potential errors, with this command `sudo pacman -S ansible-lint`. Addittional tools and programs to help with working on an Ansible project can be found [here](https://docs.ansible.com/ansible/latest/community/other_tools_and_programs.html).

## Creating a git repositiory
[Create a Github repository](https://help.github.com/en/github/getting-started-with-github/create-a-repo), and then use clone the repository to your local enviroment using the command `git clone <repo>`.

## Configuring Ansible
Create an `ansible.cfg` file in the the top directory of the repository you just cloned, and add the following to it:
```
#ansible.cfg
[defaults]
inventory = hosts.yaml
```
This will overide the default configuration file located at `/etc/ansible/ansible.cfg`, and eliminate the need for using the `-i hosts.yaml` flag and parameter when using Ansible commands.

## Creating an Invetory
Create a `hosts.yaml` file, which will serve as an [Ansible inventory](https://docs.ansible.com/ansible/2.3/intro_inventory.html), in the root directory of the repository, and add the following to it:
```
# hosts.yaml
all: 
  local:
    ansible_host: localhost
    ansible_connection: local
```
This configures a host group called `local`. The name of the host to connect to is `localhost`, and the connection to the `localhost` is `local`. Additional information on Ansible invertories can be found.

#### Pinging Hosts
Ansible can perform checks against host listed in inventory using an ['ad-hoc' command](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html), `ansible all -m ping`. Running this command should produce terminal output indicating that Ansible was able to successfully ping the hosts.

## Creating Playbooks
[Ansible Playbooks](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html) are were tasks or 'plays' are defined to maintain the state of a system. 
#### Creating Roles
[Ansible Roles](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html) help organize Ansible projects, and allow us to reuse common configuration steps. We created a Role, `roles/common/tasks`, in the root directory of the repository where we will place common playbooks.
#### Using Modules
[Ansible Modules](https://docs.ansible.com/ansible/latest/user_guide/modules.html) are built in or user created 'module libraries' that can be executed on hosts. Recall that Manjaro uses **pacman** as a package manager. Ansible has a [pacman module](https://docs.ansible.com/ansible/latest/modules/pacman_module.html) that gives us the ability to install, update, and remove packages on hosts. For example, we will use a playbook file named `system_upgrade.yaml` to upgrade the host system. The playbook contains the following contents, which we will explain further:
```
# Use the pacman module to upgrade the system
- hosts: local
  debugger: on_failed
  tasks:
    - name: upgrade all packages
      become: yes
      pacman:
        update_cache: yes
        upgrade: yes
      ignore_errors: True
```
 To run this playbook, execute the following command: `ansible-playbook roles/common/tasks/system_upgrade.yaml`.
