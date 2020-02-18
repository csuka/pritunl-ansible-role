# Pritunl for CentOS 7

This role installs Pritunl on a CentOS 7 system.
Ensure MongoDB is installed on the host(s).

When using Pritunl Enterprise, automatic failover is enabled.
In that case, the following design can be used for a cluster:

```yaml
             host1
          +---------+
          | Pritunl |
     +--->+ MongoDB +<----+
     |    +---------+     |
     |                    |
     |                    |
     |                    |
     |                    |
     v                    v
+---------+          +---------+
| Pritunl |          | Pritunl |
| MongoDB |<-------->| MongoDB |
+---------+          +---------+
   host2                host3
```


Variables
---------

 * The /etc/security/limits.conf is by default replaced with a template, to [prevent issues on high load](https://docs.pritunl.com/docs/configuration-5)
 * The config file, /etc/pritunl.conf, is not edited in any way, because all configuration of Pritunl is placed in MongoDB
 * The EPEL repo is placed on the host, because the OpenVPN package is required

The following variables can be changed, [in case of load-balancing](https://docs.pritunl.com/docs/load-balancing):

```yaml
server_port: "app.server_port = 443"
redirect_server: "app.redirect_server = false"
reverse_proxy: "app.reverse_proxy = true"
server_ssl: "app.server_ssl = false"
```

Don't place the limits file by setting:
```yaml
pritunl_limits: False
```

./ansible-playbook playbook.yml
------------------

After installation, visit the web-gui, provide the [admin credentials and set the Mongo connection string](https://docs.pritunl.com/docs/configuration-5).


**Updating** pritunl:


```bash
./ansible-playbook playbook.yml -e "pritunl_state=latest"
```

Limitations
-----------

This role **does not**

  * Manage users
  * Manage organizations / servers
  * Manage routes
  * Manage Pritunl settings
  * etc..

I've created a [Pritunl API client](https://github.com/csuka/pritunl-api-client) for this.

Example playbook
----------------

```yaml
---
- hosts:
    - host-1
    - host-2
    - host-3
  become: True
  vars:
    pritunl_state: latest
    pritunl_limits: False
    pritunl_server_port: "app.server_port = 443"
    pritunl_redirect_server: "app.redirect_server = false"
    pritunl_reverse_proxy: "app.reverse_proxy = true"
    pritunl_server_ssl: "app.server_ssl = false"
  roles:
    - pritunl-ansible-role
```
