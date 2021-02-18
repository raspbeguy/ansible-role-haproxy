HAproxy
=======

A minimalist role to install HAproxy and deploy configuration.

Requirements
------------

Nothing that Ansible hasn't already.

Role Variables
--------------

All variables are contained in `haproxy_frontends` which is the list of frontends. Each frontend has the following variables:

| Variable          | Required | Default | Description                                                     |
|-------------------|----------|---------|-----------------------------------------------------------------|
| `name`            | yes      |         | the name of the frontend                                        |
| `bind`            | no       | `'*'`   | the bind address the frontend should listen to                  |
| `http`            | no       | True    | Should the frontend respond to HTTP requests?                   |
| `https`           | no       | False   | Should the frontend respond to HTTPS requests?                  |
| `https_redirect`  | no       | True    | Should HTTP be redirected to HTTPS? Ignored if `https` is false |
| `host_strip_port` | no       | True    | Should port number be striped from `Host` header?               |
| `crt_list`        | yes      |         | the path to the file containing a list of paths to certificates |
| `sites`           | yes      |         | the list of backends to be served by the frontend               |

Each site has the following variables which are all required:

| Variable  | Description                            |
|-----------|----------------------------------------|
| `name`    | the name of the site                   |
| `fqdn`    | FQDN of the site                       |
| `servers` | the list of servers providing the site |

Each server has the following variables:

| Variable | Required | Default                  | Description                      |
|----------|----------|--------------------------|----------------------------------|
| `name`   | no       | derivated from site name | the name of the server           |
| `host`   | yes      |                          | the hostname or IP os the server |
| `port`   | no       | 80                       | the port number                  |

Example Playbook
----------------

```yaml
- name: Set up HAproxy
  hosts: proxy
  roles:
    - role: haprosy
      haproxy_frontends:
        - name: frontoffice
          bind: '1.2.3.4'
          http: True
          https: True
          https_redirect: True
          crt_list: /etc/haproxy/crt-list-front.txt
          sites:
            - name: homepage
              fqdn: www.example.com
              servers:
                - host: 192.168.1.1
            - name: shop
              fqdn: shop.example.com
              servers:
                - host: 192.168.2.1
                - host: 192.168.2.2
                - host: 192.168.2.3
        - name: backoffice
          bind: '1.2.3.5'
          http: True
          https: True
          https_redirect: True
          crt_list: /etc/haproxy/crt-list-back.txt
          sites:
            - name: webeditor
              fqdn: admin.www.example.com
              servers:
                - host: 192.168.11.1
            - name: shopeditor
              fqdn: admin.shop.example.com
              servers :
                - host: 192.168.2.1
                  port: 8080
```

License
-------

BSD
