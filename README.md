# AmneziaWG VPN Auto-Configuration with Ansible

Automated WireGuard-based VPN server provisioning using AmneziaWG. This repository contains production-ready Ansible playbooks to deploy and manage a secure AmneziaWG VPN server in minutes.

# Features

- **AmneziaWG Installation** - Automated deployment of AmneziaWG with optimal defaults
- **User Management**- Simple CLI to add or revoke VPN users with isolated configs
- **Multi-User Support** - Independent VPN profiles for unlimited users
- **Easy Maintenance** - Idempotent playbooks for updates and configuration changes
- **Client Configuration Generation** - Automatic generation .conf files for easy client setup

# Quick Start

## Prerequisites
- Ansible core 2.16.3+ installed on your local machine
- SSH access to your VPS/VDS server && installed sshpass package 
- Python 3.x on the target server

## Usage
```bash
git clone https://github.com/dimasikks/AmneziaWG_VPN_configuration.git awgc
cd awgc
```
### Install

To install AmneziaWG to your server you need to **configurate inventory and vars file in install role**, after that run command below:
```bash
ansible-playbook playbooks/install.yml
```
This will install AmneziaWG VPN to your server

### Adding Users

To give someone the opportunity to connect you need to **configurate vars file in add_user role**, after that run command below:
```bash
ansible-playbook playbooks/add_user.yml -e "USER_ID=<USER_ID> USER_IP_OCTET=<USER_IP_OCTET>"
```
#### Explanation

**USER_ID** - unique user identity for your server
**USER_IP_OCTET** - unique user IP at server, ex: 11.1.1.5, 11.1.1.25 - **5, 25 - USER_IP_OCTET**

The **USER_IP_OCTET** depends on the variables configuration.awg_ip from **install role** and on user_configuration.server_net from **add_user role**

**EXAMPLE**
```bash
ansible-playbook playbooks/add_user.yml -e "USER_ID=testing_user USER_IP_OCTET=25"
```
This will create user with IP=11.1.1.25, **if user_configuration.server_net**: "11.1.1."

### Deleting Users

To delete user from AmneziaWG VPN server, you need to **configurate vars file in del_user role**, after that run command below:
```bash
ansible-playbook playbooks/del_user.yml -e "USER_ID=<USER_ID>"
```

## Project structure
```bash
awgc/
├── ansible.cfg                                    # default ansible parameters (change, if you want)
├── inventory                                      # host inventory (change)
├── playbooks
│   ├── add_user.yml                               # add user playbook
│   ├── del_user.yml                               # del user playbook
│   └── install.yml                                # main installation playbook
├── roles
│   ├── add_user                                   # role for adding
│   │   ├── tasks
│   │   │   ├── create_user_config.yml
│   │   │   ├── generate_user_keys.yml
│   │   │   ├── main.yml
│   │   │   └── update_server_config.yml
│   │   ├── templates
│   │   │   └── awg_user.primary                   # awg user config
│   │   └── vars
│   │       └── main.yml                           # vars for user awg config (change)
│   ├── del_user                                   # role for deleting
│   │   ├── tasks
│   │   │   ├── delete_users.yml
│   │   │   └── main.yml
│   │   └── vars
│   │       └── main.yml                           # vars for user awg delete config (change if you want)
│   └── install                                    # role for installing
│       ├── tasks
│       │   ├── configuration.yml
│       │   ├── installation.yml
│       │   └── main.yml
│       ├── templates
│       │   ├── awg.primary                        # main AmneziaWG configuration file (change)
│       │   └── iptables.primary                   # iptables primary config (change, if you want)
│       └── vars
│           └── main.yml                           # main vars for installation (change)
├── server_koara_keyfile                           # key for access to server
└── USERS_configs                                  # users configs after running add_user playbook
    ├── test1_config                               # example of configuraion to connect to hosted AmneziaWG
    └── test2_config
```
