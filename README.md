# Users
Ansible role to create/configure users on Linux/FreeBSD 

## Variables
| user_groups | 
| ---         | 
| name        | name of the group                             |
| gid         | group ID                                      |   
| state       | whether the group shoud be created or removed |
| users       |

## Default variables
The default shells depending on the OS are:

- Linux: `/bin/bash`
- FreeBSD: `/bin/cshrc`

This is defined in the `defaults` section of the **users roles**


## Example Playbook

```
user_groups:
  - name: mygroup
    gid: 700
    

users:
  - name: remember
    state: present
    password: "blabla"
    groups:
      - mygroup
    uid: 1100
    keys:
      - file: key1
        state: present
    shell_lines:
      - line: "export SSH_AUTH_SOCK=$HOME/.gnupg/S.gpg-agent.ssh"
        state: present
      - line: "alias ls='ls lah'"
        state: present
  - name: test
    keys:
      - file: key2
        state: absent
    shell_lines:
      - line: "export SSH_AUTH_SOCK=$HOME/.gnupg/S.gpg-agent.ssh"
        state: absent
```
## Using the Role
### Adding user

### Configure users' shell

https://github.com/stationgroup/ansible-experiments/issues/9
