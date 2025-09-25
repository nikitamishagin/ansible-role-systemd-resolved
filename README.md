# Systemd-Resolved Ansible Role

## Description

A fork of the [idiv_biodiversity.systemd_resolved](https://github.com/idiv-biodiversity/ansible-role-systemd-resolved) role (by Christian Krause).

This role manages the systemd-resolved service on Linux hosts. It supports two operating modes:

- Install and configure (default)
- Disable and remove

In the default mode the role performs the following steps:

- installs the systemd-resolved package;
- renders /etc/systemd/resolved.conf from a template;
- enables and starts the service;
- creates (or re-creates) the symlink /etc/resolv.conf to /run/systemd/resolve/stub-resolv.conf.

## How to use

The role uses the vendor-agnostic ansible.builtin.package module and therefore supports many Linux distributions with
systemd and the systemd-resolved package. Below are a couple of examples of using this role in a playbook.

### Install and configure

```yaml
- hosts: all
  roles:
    - role: scanfactory.systemd-resolved
      vars:
        systemd_resolved_servers:
          - 1.1.1.1#cloudflare-dns.com
          - 8.8.8.8#dns.google
        systemd_resolved_fallback_servers:
          - 9.9.9.9#dns.quad9.net
        systemd_resolved_domains:
          - corp.example.com
          - example.com
        systemd_resolved_dnssec: false
        systemd_resolved_dns_over_tls: true
        systemd_resolved_multicast_dns: false
        systemd_resolved_llmnr: false
        systemd_resolved_cache: true
        systemd_resolved_cache_from_localhost: false
        systemd_resolved_dns_stub_listener: true
        systemd_resolved_dns_stub_listener_extra: "127.0.0.54:53"
        systemd_resolved_read_etc_hosts: true
        systemd_resolved_resolve_unicast_single_label: false
        systemd_resolved_stale_retention_sec: 0
```

### Disable and remove

```yaml
- hosts: all
  roles:
    - role: scanfactory.systemd-resolved
      vars:
        systemd_resolved_remove: true
```

## Variables

Default values are shown as defined in the role. If "not set", the option is omitted from
/etc/systemd/resolved.conf and systemd's defaults are used.

| Variable                                       | Default               | Description                                                                                                                                            |
|-----------------------------------------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemd_resolved_remove                       | false                 | Role mode: false — install and configure; true — stop the service and remove the package.                                                              |
| systemd_resolved_servers                      | not set               | List of DNS servers for DNS=. May be a string or a list. SNI names via # are supported (e.g., 1.1.1.1#cloudflare-dns.com).                            |
| systemd_resolved_fallback_servers             | not set               | List of fallback DNS servers for FallbackDNS=. String or list.                                                                                         |
| systemd_resolved_domains                      | not set               | List of search domains for Domains=. String or list.                                                                                                   |
| systemd_resolved_dnssec                       | not set               | DNSSEC control: yes, no, allow-downgrade. If a boolean is provided, the template converts it to yes/no.                                               |
| systemd_resolved_dns_over_tls                 | not set               | Enable DNS-over-TLS: yes, opportunistic, or no. Boolean values are converted to yes/no.                                                                |
| systemd_resolved_multicast_dns                | not set               | Configure MulticastDNS: yes, no, resolve, or resolve-mdns. Boolean values are converted to yes/no.                                                     |
| systemd_resolved_llmnr                        | not set               | Configure LLMNR: yes, no, or resolve. Boolean values are converted to yes/no.                                                                          |
| systemd_resolved_cache                        | not set               | Resolver cache: yes or no. Boolean values are converted to yes/no.                                                                                     |
| systemd_resolved_cache_from_localhost         | not set               | Allow caching of queries from localhost: yes or no. Boolean values are converted to yes/no.                                                            |
| systemd_resolved_dns_stub_listener            | not set               | Built-in DNS stub listener mode: yes, no, udp, tcp. Boolean values are converted to yes/no.                                                            |
| systemd_resolved_dns_stub_listener_extra      | not set               | Additional address for DNSStubListenerExtra=, e.g., "127.0.0.54:53". String.                                                                          |
| systemd_resolved_read_etc_hosts               | not set               | Read /etc/hosts: yes or no. Boolean values are converted to yes/no.                                                                                    |
| systemd_resolved_resolve_unicast_single_label | not set               | Allow unicast resolution of single-label names: yes or no. Boolean values are converted to yes/no.                                                     |
| systemd_resolved_stale_retention_sec          | not set               | Retention time for stale entries in the cache (seconds) for StaleRetentionSec=. Integer.                                                                |

Notes:

- Variables can be provided as strings or lists (where applicable). The template will join a list into a space-
  separated string.
- For boolean variables the template automatically substitutes yes/no.
- If a variable is not set, the corresponding line will be commented out or omitted, and systemd will apply its
  defaults.
