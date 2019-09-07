HAproxy
=======

A minimalist role to install HAproxy and deploy configuration.

Requirements
------------

Nothing that Ansible has already.

Role Variables
--------------

All variables are contained in `haproxy_frontends` which is the list of frontends. Each frontend has the following variables:

| Variable        | Required | Default | Description                                        |
|-----------------|----------|---------|----------------------------------------------------|
| `name`          | yes      |         | the name of the frontend                           |
| `bind`          | no       | `'*'`   | the bind address the frontend should listen to     |
| `http`          | no       | True    | Should the frontend respond to HTTP requests?      |
| `https`         | no       | True    | Should the frontend respond to HTTP re

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
