# Ansible Role backuppc

This role installs and configures the [BackupPC](https://backuppc.github.io/backuppc/) server.

## Role Variables

### System

| Variable name                   | Type    | Default | Description                                             |
| ------------------------------- | ------- | ------- | ------------------------------------------------------- |
| backuppc_service_user | string | backuppc | The admin and service user for backuppc. |
| backuppc_service_password | string | password | The admin password to use |
| backuppc_web_user | string | `{{ backuppc_service_user }}` | The user to use for the apache process.* |
| backuppc_web_group | string | `{{ backuppc_service_user }}` | The group to use for the apache process.* |
| backuppc_host | string | backuppc.example | The host name to configure for backuppc web interface. |
| backuppc_aliases | list[string] |  | Alias host names for the backuppc web interface. |
| backuppc_http | bool | true | Flag to enable/disable HTTP availability of the web interface. |
| backuppc_ssl | bool | false | Flag to enable/disable HTTPS availability of the web interface.** |
| backuppc_htpasswd_path | path | /etc/backuppc/htpasswd | Location of the htpasswd file that should be used for authorization. |
| backuppc_web_enabled | bool | true | Flag that can be used to disable/enable the web interface. |
| backuppc_vhost_template | path | templates/backuppc-vhost.conf.j2 | Allows replacing the default apache vhost template. |
| backuppc_hosts_template | path | templates/etc/backuppc/hosts.j2 | Allows replacing the default template of the backuppc hosts configuration. |
| backuppc_host_template | path | templates/etc/backuppc/host.pl.j2 | Allows replacing the default host configuration template. |

*It is recommended to run the apache process as the service user, since access privileges might break otherwise.  
**SSL is configured by the [apache2](https://git-service.ait.ac.at/sct-cyberrange/ansible-roles/apache2) (see details) role the required variables are made available with the prefix `backuppc_` (e.g., `backuppc_cert`). Certs are always copied from the host.  


### Backups

| Variable name                   | Type    | Default | Description                                             |
| ------------------------------- | ------- | ------- | ------------------------------------------------------- |
| backuppc_hosts | list[dict] | [] | List of hosts and settings to configure for backups. Dictionary keys are listed below |
| host | string |  | The hostname or IP for the backup target. |
| **users** | list[dict] | [] | Lists of users which should be able to access and configure the backup target using the web interface. The admin will always be the main user. |
| &nbsp;∟.user | string |  | The username of the web interface user |
| &nbsp;∟.password | string |  | The username of the web interface user |
| **sync** | dict |  | Dictionary containing the backup configuration for the backup target |
| &nbsp;∟.auth | bool | true | Flag used to define if authorization is required. |
| &nbsp;∟.user | string |  | The username that should be used to authorize with the remote rsyncd daemon. |
| &nbsp;∟.password | string |  | The password that should be used to authorize with the remote rsyncd daemon. |
| &nbsp;∟.shares | list[string] |  | List of shares to backup |
| &nbsp;∟.only_files | list[string] |  | List used to limit the files/directories that will be synced from the shares. |
| &nbsp;∟.full_period | float | 0.97  | The frequency (days) in which full backups should be made.  |
| &nbsp;∟.incr_period | float | 0.24 | The frequency (days) in which incremental backups should be made. |
| dhcp | 0/1 | 0 | The dhcp value for the host (see [backuppc](http://backuppc.sourceforge.net/faq/BackupPC.html#Step-4:-Setting-up-the-hosts-file) for details) |


## Dependencies

The role depends on the Cyber Range [apache2](https://git-service.ait.ac.at/sct-cyberrange/ansible-roles/apache2) role which can be installed through the requirements file.

```console
ansible-galaxy install -r requirements.yml
```

## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
    - hosts: servers
      roles:
         - role: backuppc
           vars:
             backuppc_hosts: 
              - host: 192.168.56.101
                users: 
                  - user: test
                    password: test
                  - user: test2
                    password: test2
                sync:
                  user: backup
                  password: backup
                  shares: 
                      - backuppc
                      - apache2
```

## License

GPL-3.0

## Author Information

Maximilian Frank
