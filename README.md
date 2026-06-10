# ansible-users

An [Ansible](https://www.ansible.com) role to manage local users

## Requirements

None

## Role Variables

[defaults/main.yml](defaults/main.yml)

| Variable | Default | Description |
|----------|---------|-------------|
| `users_manage_accounts` | `false` | Enable user account management tasks |
| `users_manage_samba_accounts` | `false` | Enable Samba account registration via `smbpasswd` for users that have a `samba_password` field |
| `users_smbpasswd_package` | `samba-common-bin` (Debian) / `samba-common` (RHEL) | Package that provides the `smbpasswd` binary; resolved automatically from `ansible_os_family` |
| `users` | `{}` | Dict of users to manage (see example below) |

### Samba Integration

Set `users_manage_samba_accounts: true` and add a `samba_password` field to any user that should be registered with `smbpasswd`. The role will:

- Install `smbpasswd` (package resolved per OS family)
- Register each user that has `samba_password` defined and `state: present`
- Run `smbpasswd -x` to remove Samba accounts for users with `state: absent`

Passwords are passed to `smbpasswd` via environment variable, not shell interpolation. All password-handling tasks run with `no_log: true` and will not appear in Ansible output.

**Example user definition with Samba password:**

```yaml
users_manage_accounts: true
users_manage_samba_accounts: true

users:
  demo-user-01:
    comment: Demo user 01
    generate_keys: true
    password: demo_password
    samba_password: samba_demo_password
    state: present
    sudo: false
    system_account: false
  demo-user-02:
    comment: Demo user 02
    generate_keys: true
    password: demo_password
    state: absent        # removes system account and Samba account (smbpasswd -x)
    sudo: false
    system_account: false
```

Users without a `samba_password` field are unaffected by `users_manage_samba_accounts`.

## Dependencies

None

## Example Playbook

[playbook.yml](playbook.yml)

## License

MIT

## Author Information

Larry Smith Jr.

-   [EverythingShouldBeVirtual](http://everythingshouldbevirtual.com)
-   [@mrlesmithjr](https://www.twitter.com/mrlesmithjr)
-   mrlesmithjr [at] gmail.com
