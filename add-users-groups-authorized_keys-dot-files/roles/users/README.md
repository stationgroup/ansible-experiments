# Users
Ansible roles to create/configure users on Linux/FreeBSD.

## Variables 
| user_groups  |                                               |                |
| ---:         |---                                            |---             |
| name         | name of the group                             | Data type      |
| gid          | Optionally set the group ID                   | int            |
| state        | whether the group shoud be created or removed | present/absent |


| users            |   |    |
| ---:             |---| ---|
| _variable name_    | Description                                   | Data type       | 
| name             | username                                      | string          |
| state            | whether the user should be created or removed | present/absent  |
| password         | string of an encrypted value(1)               | string          |
| groups           | additional groups the user should belong to   | list            |
| uid              | optionally specify a user id                  | int             |
| enable_sudo      | Enable passwordless sudo for the given user   | bool            |
| keys             | list of dictionaries                          | list            |
| bash_lines       | configure lines in .bashrc                    | list            |
| bash_blocks      | configure lines in .bashrc                    | list            |
| csh_lines        | configure lines in .cshrc                     | list            |
| csh__blocks      | configure lines in .cshrc                     | list            |

(1) https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#how-do-i-generate-crypted-passwords-for-the-user-module

## Default variables
The default shells depending on the OS are:

- Linux: `/bin/bash`
- FreeBSD: `/bin/cshrc`

This is defined in the `defaults` section of the **users** role


## Example inventory
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
    enable_sudo: true
    keys:
      - file: key1
        state: present
    bash_lines:
      - line: 'export SSH_AUTH_SOCK="${HOME}/.gnupg/S.gpg-agent.ssh"'
        state: present
      - line: "alias ls='ls lah'"
        state: present
    bash_blocks:
      - content: |
          #testing
          #multiline
        state: present
  - name: test
    enable_sudo: false
    keys:
      - file: key2
        state: absent
    csh_lines:
      - line: "ls ls -lah"
        state: absent
```
## Using the Role
### Example Playbook
```
---
- name: Manage user configuration
  hosts: all
  remote_user: root
  roles:
    - users
```
### Configure a user's ssh keys
For every user a directory matching the username should be created under the _keys_ folder in the role's _files_ folder. In this folder the user's ssh keys can be stored.

```
├── files
│   └── keys
│       ├── remember
│       │   └── key1.pub
│       └── test
│           └── key2.pub
```
The name of the file holding the key should match the name in the _users_ variable

```
    keys:
      - file: key1
        state: present
```

### Configure a user's shell
This role allows you to add or remove lines to a user's `.bashrc` or `cshrc` file. Since this is not based on a template that overwrites the complete file, users can still add their own configuration too.

Add items to the **shell_lines** key in the **users** variable. Each item exists of a _line_ and _state_ key.

**lines**

Use _lines_ if you want to make sure a single line is present or not.
Example:
```
shell_lines:
  - line: "testline"
    state: absent
  - line: 'export SSH_AUTH_SOCK="${HOME}/.gnupg/S.gpg-agent.ssh"'
    state: present
  - line: "alias ls='ls lah'"
    state: present
```

**blocks**

use blocks if you want to make sure a number of lines that belong together are
present or not.

Example:
```
bash_blocks:
  - content: |
      if [ condition ]; then
        do something
    state: present
```
