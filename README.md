php7-fpm
========

This role based on https://github.com/NBZ4live/ansible-php-fpm

This role installs and configures the php7-fpm interpreter.

Requirements
------------

This role requires Ansible 1.4 or higher and tested platforms are listed in the metadata file.

Role Variables
--------------

The role uses the following variables:

 - **php7_fpm_pools**: The list a pools for php7-fpm, each pools is a hash with
   a name entry (used for filename), all the other entries in the hash are pool
   directives (see http://php.net/manual/en/install.fpm.configuration.php).
 - **php7_fpm_pool_defaults**: A list of default directives used for all php7-fpm pools
   (see http://php.net/manual/en/install.fpm.configuration.php).
 - **php7_fpm_apt_packages**: The list of packages to be installed by the
  ```apt```, defaults to ```[php7.1-fpm]```.
   module.
 - **php7_fpm_ini**: Customization for php7-fpm's php.ini as a list of options,
   each option is a hash using the following structure:
     - **option**: The name of the option.
     - **value**: The string value to be associated with the option.
     - **section**: Section name in INI file.
 - **php7_fpm_config**: Customization for php7-fpm's configuration file as a list
   of options.
 - **php7_fpm_apt_packages**: The APT packages to install, defaults to ```[php7.1-fpm]```.
 - **php7_fpm_default_pool**:
     - **delete**: Set to a ```True``` value to delete the default pool.
     - **name**: The filename the default pool configuration file.

Example configuration
--------------

    - role: php7-fpm
      php7_fpm_pool_defaults:
        pm: dynamic
        pm.max_children: 5
        pm.start_servers: 2
        pm.min_spare_servers: 1
        pm.max_spare_servers: 3
      php7_fpm_pools:
       - name: foo
         user: www-data
         group: www-data
         listen: 8000
         chdir: /
       - name: bar
         user: www-data
         group: www-data
         listen: 8001
       php7_fpm_ini:
       # PHP section directives
       - option: "engine"
         section: "PHP"
         value: "1"
       - option: "error_reporting"
         section: "PHP"
         value: "E_ALL & ~E_DEPRECATED & ~E_STRICT"
       - option: "date.timezone"
         section: "PHP"
         value: "Europe/Berlin"
       # soap section directives
       - option: "soap.wsdl_cache_dir"
         section: "soap"
         value: "/tmp"
       # Pdo_mysql section directives
       - option: "pdo_mysql.cache_size"
         section: "Pdo_mysql"
         value: "2000"
       php7_fpm_config:
       - option: "log_level"
         section: "global"
         value: "notice"
       - option: "syslog.facility"
         section: "global"
         value: "daemon"

Example usage
-------

    ---
    # file: task.yml
    - hosts: all
      roles:
        - nbz4live.php7-fpm
        - {
            role: nbz4live.php7-fpm,
            php7_fpm_pools:[
              {name: foo, user: www-data, group: www-data, listen: 8000, chdir: /}
            ]
          }
        - role: php7-fpm
            php7_fpm_pools:
              - name: bar
                user: www-data
                group: www-data
                listen: 9000
                chdir: /

Attention
-------
The process manager configuration (pm, pm.max_children, pm.start_servers, pm.min_spare_servers, pm.max_spare_servers),
in the defaults, is only for testing. This values should always be calculated based on the used server resources
(hardware, number of pools, other software on the server).
Please read the [documentation](http://php.net/manual/en/install.fpm.configuration.php#pm) for more information
about this directives or follow [this guide](http://myshell.co.uk/blog/2012/07/adjusting-child-processes-for-php7-fpm-nginx/)
to calculate best values for your case.

License
-------

BSD

Author Information
------------------

- Sergey Fayngold <sergey@faynhost.com>
- Pierre Buyle <buyle@pheromone.ca>
