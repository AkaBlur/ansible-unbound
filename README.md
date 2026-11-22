# Ansible UnBound

Ansible role to install UnBound on the target hosts. Configures basic settings
and sets up a complete recursive DNS resolver.

Most common settings are done via the [DNS](#dns) configuration entries.

## Role Variables

### Basic Configuration

**`unbound_conf_rmt_control`** - `bool` - Default `false`

- Controls if remote control should be made available

**`unbound_conf_interface`** - `string` - Default "0.0.0.0"

- unbound listening interface
- Defaults to all interfaces on the host

### Security

**`unbound_conf_security_harden`** - `bool` - Default `true`

- Enables basic security hardening for installation
  - Enable QNAME minimization

**`unbound_conf_allow_ip_ranges`** - list/`string`

- List of allowed IP ranges for access to unbound instance
- Default is to reject incoming DNS queries
- Given as list of IP range strings

```yaml
unbound_conf_allow_ip_ranges:
  - "10.0.0.0/24"
  - "10.10.0.0/24"
  - "10.10.0.10/24"
```

### Performance

**`unbound_conf_serve_stale`** - `bool` - Default `true`

- Enable serving of stale DNS data
- Increase cache-hits for unbound
- Enables faster queries (most commonly)
- Stale data limit set to default of 4 h (limit)

### DNS

**`unbound_conf_local_zone`** - list/zone_object

- List of zone modifier entries
- Enable overrides for zones used in request queries
- Given as object with `zone` and `rule` entry
- E.g. enable override for local zone entries:

```yaml
# sets .internal zone as nondefault zone (e.g. for LAN usage)
unbound_conf_local_zone
  - { zone: ".internal", rule: "nodefault" }
```

**unbound_conf_local_data** - list/`string`

- Add custom DNS entries for resolver
- Added as DNS entry string

```yaml
unbound_conf_local_data:
  - "admin.internal A 10.10.11.10"
```

## Example Usage

Basic role example for a custom playbook:

```yaml
- name: Example usage
  hosts: localhost
  roles:
    - role: AkaBlur.unbound
      vars:
        unbound_conf_interface: "0.0.0.0"
        unbound_conf_allow_ip_ranges:
          - "10.10.0.0/24"
        unbound_conf_local_zone:
          - { zone: ".internal", rule: "nodefault" }
        unbound_conf_local_data:
          - "admin.internal A 10.10.0.5"
          - "web.internal A 10.10.0.10"
```
