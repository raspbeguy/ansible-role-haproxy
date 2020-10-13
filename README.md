HAproxy
=======

A minimalist role to install HAproxy and deploy configuration.

Requirements
------------

Nothing that Ansible hasn't already.

Role Variables
--------------

### Frontends

The variable `haproxy_frontends` is the list of frontends. Each frontend has the following variables:

| Variable         | Required | Default | Description                                                     |
|------------------|----------|---------|-----------------------------------------------------------------|
| `name`           | yes      |         | the name of the frontend                                        |
| `bind`           | no       | `'*'`   | the bind address the frontend should listen to                  |
| `http`           | no       | True    | Should the frontend respond to HTTP requests?                   |
| `https`          | no       | True    | Should the frontend respond to HTTPS requests?                  |
| `https_redirect` | no       | True    | Should HTTP be redirected to HTTPS?                             |
| `crt_list`       | yes      |         | the path to the file containing a list of paths to certificates |
| `backends`       | yes      |         | the list of backends to be served by the frontend               |

Each backend has the following variables:

| Variable    | Required | Default | Description                                                 |
|-------------|----------|---------|-------------------------------------------------------------|
| `name`      | yes      |         | the name of the site                                        |
| `fqdn`      | yes      |         | FQDN of the site                                            |
| `userlists` | no       | none    | a facultative list of userlists names (see [](#user-lists)) |
| `servers`   | yes      |         | the list of servers providing the site                      |

Each server has the following variables:

| Variable | Required | Default                  | Description                      |
|----------|----------|--------------------------|----------------------------------|
| `name`   | no       | derivated from site name | the name of the server           |
| `host`   | yes      |                          | the hostname or IP os the server |
| `port`   | no       | 80                       | the port number                  |

### User lists

To protect some sites with basic auth, user lists can be defined in list `haproxy_userlists`. Each user list has the following mandatory variables:

| Variable | Description                                          |
|----------|------------------------------------------------------|
| `name`   | the name of the userlist refered in the backend list |
| `users`  | the list of contained users                          |

Each user is a dictionnary containing those variables:

| Variable   | Required | Default | Description                                               |
|------------|----------|---------|-----------------------------------------------------------|
| `name`     | yes      |         | the user name                                             |
| `password` | yes      |         | the user password (recommended to use a lookup here)      |
| `hash`     | no       | True    | Should the passsword be hash protected in haproxy config? |
| `hash_alg` | no       | sha512  | how to hash the password in haproxy config                |

Example Playbook
----------------

```yaml
- name: Set up HAproxy
  hosts: proxy
  roles:
    - role: haproxy
      haproxy_userlists:
        - name: admins
          users:
            - name: homer
              password: d0nut5
      haproxy_frontends:
        - name: frontoffice
          bind: '1.2.3.4'
          http: True
          https: True
          https_redirect: True
          crt_list: /etc/haproxy/crt-list-front.txt
          backends:
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
          backends:
            - name: webeditor
              fqdn: admin.www.example.com
              userlists:
                - admins
              servers:
                - host: 192.168.11.1
            - name: shopeditor
              fqdn: admin.shop.example.com
              userlists:
                - admins
              servers :
                - host: 192.168.2.1
                  port: 8080
```

License
-------

BSD
