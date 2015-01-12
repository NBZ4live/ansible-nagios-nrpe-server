[![Build Status](https://travis-ci.org/NBZ4live/ansible-nagios-nrpe-server.png?branch=master)](https://travis-ci.org/NBZ4live/ansible-nagios-nrpe-server)

ansible-nagios-nrpe-server
========

This role installs and configures the ansible-nagios-nrpe-server.

Requirements
------------

This role requires Ansible 1.4 or higher and tested platforms are listed in the metadata file.

Role Variables
--------------

The role uses the following variables:

 - **nagios_nrpe_server_config_d_name**: Name of the config file to create in the "nrpe.d",
  defaults to ```50_ansible.cfg```. (must end with .cfg to work!)
 - **nagios_nrpe_server_apt_packages**: The list of packages to be installed by the
  ```apt``` module, defaults to ```[nagios-nrpe-server, nagios-plugins-extra]```.
 - **nagios_nrpe_server_config**: Customization for nrpe's configuration file as a list
   of options.

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
        
License
-------

BSD

Author Information
------------------

- Sergey Fayngold <sergey@faynhost.com>
