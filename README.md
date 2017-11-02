[![Build Status](http://img.shields.io/travis/NBZ4live/ansible-nagios-nrpe-server.svg?style=flat)](https://travis-ci.org/NBZ4live/ansible-nagios-nrpe-server) [![Ansible Galaxy](http://img.shields.io/badge/ansible--galaxy-nagios--nrpe--server-blue.svg?style=flat)](https://galaxy.ansible.com/list#/roles/2567)

nagios-nrpe-server
========

This role installs and configures the nagios-nrpe-server.

Requirements
------------

This role requires Ansible 2.0 or higher and tested platforms are listed in the metadata file.

Role Variables
--------------

The role uses the following variables:

 - **nagios_nrpe_server_config_d_name**: Name of the config file to create in the "nrpe.d",
  defaults to ```50_ansible.cfg```. (must end with .cfg to work!)
 - **nagios_nrpe_server_apt_packages**: The list of packages to be installed by the
  ```apt``` module, defaults to ```[nagios-nrpe-server, nagios-plugins-extra]```.
 - **nagios_nrpe_server_config**: Customization for nrpe's configuration file as a list
   of options.
 - **nagios_nrpe_server_commands**: Configuration of custom commands.
 - **nagios_nrpe_server_sudo_commands**: Configuration of commands for the sudoers file.

Global Variables
--------------
This variables are without a namespace to be used across the entire playbook
 - **apt_cache_valid_time**: Used for ```cache_valid_time``` in the apt module.
  Defaults to ```86400```

Example configuration
--------------

    nagios_nrpe_server_config_d_name: 50_ansible.cfg
    nagios_nrpe_server_apt_packages:
      - nagios-nrpe-server
      - nagios-plugins-extra
    
    nagios_nrpe_server_config:
      server_port: 5666
      allowed_hosts: 127.0.0.1

Example usage
-------

    ---
    # file: task.yml
    - hosts: all
      roles:
        - nbz4live.nagios-nrpe-server
        - {
            role: nbz4live.nagios-nrpe-server,
            nagios_nrpe_server_config_d_name: mynrpe.cfg
            nagios_nrpe_server_apt_packages:[
              nagios-nrpe-server,
              nagios-plugins-extra,
              nagios-plugins-contrib
            ],
            nagios_nrpe_server_config: {
              command[check_load]: "/usr/lib/nagios/plugins/check_load -w 30,25,20 -c 45,40,35",
              command[check_custom_directory_disk]: /usr/lib/nagios/plugins/check_disk -w 15% -c 10% -p /path/to/my/directory
            } 
          }
        - role: nbz4live.nagios-nrpe-server
            nagios_nrpe_server_config:
              command[check_load]: /usr/lib/nagios/plugins/check_load -w 30,25,20 -c 45,40,35
              command[check_custom_directory_disk]: /usr/lib/nagios/plugins/check_disk -w 15% -c 10% -p /path/to/my/directory
            nagios_nrpe_server_commands:
              - name: check_users
                line: /usr/lib/nagios/plugins/check_users -w 5 -c 10
              - name: check_load
                plugin: check_load
                args: -w 15,10,5 -c 30,25,20
              - name: check_hda1
                plugin: check_disk
                args: -w 20% -c 10% -p /dev/hda1
                sudo: true
            nagios_nrpe_server_sudo_commands:
              - /usr/lib/nagios/plugins/check_procs
              - check_mysql

Command configuration
-------
This role implements different types of command definition.
You can define them as:

1. Configuration lines in ```nagios_nrpe_server_config```
2. Command lines in ```nagios_nrpe_server_commands```
3. Plugins in ```nagios_nrpe_server_commands```

### nagios_nrpe_server_config
    nagios_nrpe_server_config:
      command[check_load]: /usr/lib/nagios/plugins/check_load -w 30,25,20 -c 45,40,35
      command[check_custom_directory_disk]: sudo /usr/lib/nagios/plugins/check_disk -w 15% -c 10% -p /path/to/my/directory

If a command was defined as a configuration line but needs to be added to the sudoers file
nagios_nrpe_server_sudo_commands can be used.

    nagios_nrpe_server_sudo_commands:
      - /usr/lib/nagios/plugins/check_procs
      - check_mysql
This will add the following to the file in sudoers.d:

    nagios ALL=NOPASSWD: /usr/lib/nagios/plugins/check_procs
    nagios ALL=NOPASSWD: /usr/lib/nagios/plugins/check_mysql

### nagios_nrpe_server_commands with command lines
    nagios_nrpe_server_commands:
      - name: check_users
        line: /usr/lib/nagios/plugins/check_users -w 5 -c 10

### nagios_nrpe_server_commands with plugin
    nagios_nrpe_server_commands:
      - name: check_hda1
        plugin: check_disk
        args: -w 20% -c 10% -p /dev/hda1
        sudo: true

License
-------

BSD

Author Information
------------------

- Sergey Fayngold <sergey@faynhost.com>
