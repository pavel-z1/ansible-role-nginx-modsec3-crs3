# Ansible role for compiling ModSecurity3, and installing the OWASP CRS v3 ruleset 
## Can be used with official Nginx role ansible-role-nginx-modsec3-crs3

ModSecurity3 is a powerful open source cross-platform web application firewall (WAF).

https://modsecurity.org/

It goes hand in hand with a ruleset known as OWASP CRS.

https://modsecurity.org/crs/

Additionally both of these go hand in hand with a webserver, either Apache or Nginx, this role only supports Nginx however.

https://www.nginx.com

There are a number of libraries and packages which ModSecurity3 depends on and will be installed via this role.

This role will additionally install any compilers and other build tools required for compilation. It will then remove these tools if they were not previously installed. 

This role will no install Nginx packages.

## Requirements

You must already have Nginx server installed
This role will determine  Nginx installed version and will compile ModSecurity based on your Nginx version

## Role Variables

Browse the role's [defaults/main.yml](defaults/main.yml) and [vars/main.yml](vars/main.yml) files to see if there is anything you would like to change or need to override by setting in your playbook.

There are currently no variables of note being set.

There are lots of variables however in the nginx role, perhaps the best explanation of these are all the examples in the role [README.md](https://github.com/jdauphant/ansible-role-nginx/blob/master/README.md) file.


## Example Playbook

Example playbook calling the role adding and enabling ModSecurity for the default Nginx site.
In this example, vars used to configure Nginx server with official role https://github.com/nginxinc/ansible-role-nginx

```yaml
- hosts: servers

  vars:
    nginx_main_template_enable: true
    nginx_main_template:
      template_file: nginx.conf.j2
      conf_file_name: nginx.conf
      conf_file_location: /etc/nginx/
      user: nginx
      worker_processes: auto
      ...
      custom_options:
        - load_module modules/ngx_http_modsecurity_module.so


    nginx_http_template_enable: true
    nginx_http_template:
      default:
        template_file: http/default.conf.j2
        conf_file_name: "config_name.conf"
        conf_file_location: /etc/nginx/conf.d/
        servers:
          zabbix_dashboard_https:
            listen:
              listen_localhost:
                port: 80
                opts:
                  - default_server
            server_name: "{{ ansible_fqdn }} zabbix.observe.kwebbl.cloud www.zabbix.observe.kwebbl.cloud"
            error_page: /usr/share/nginx/html
            autoindex: false
            root: /usr/share/zabbix
            custom_options:
              - modsecurity on
              - modsecurity_rules_file /etc/nginx/modsec/main.conf

  roles:
    - ansible-role-nginx-modsec3-crs3
```

Key options in this example:
For Nginx Main config:
```
  custom_options:
    - load_module modules/ngx_http_modsecurity_module.so
```

For Nginx Server/Vhost config:
```
  custom_options:
    - modsecurity on
    - modsecurity_rules_file /etc/nginx/modsec/main.conf
```

# License

MIT

## Author Information

Perry Kollmorgen - https://github.com/perryk

