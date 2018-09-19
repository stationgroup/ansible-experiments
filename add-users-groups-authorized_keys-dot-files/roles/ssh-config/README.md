# ssh-config
Ansible role to configure a user's `~/.ssh/config` file. This will add a
configuration in the ssh config file for each host in the inventory.

**NOTE: this role works in conjunction with the _users_ variable**

## Variables 

| _variable name_   | Description                                   | 
| ---:            |---                                            |
| ssh_short_name  | host identifier name in the ssh config.<br>This should be added to the _host variables_  |
| ssh_config      | name of the key in the *users* variable. Contains a list of
key/value items| 

## Example:

**Host inventory**
```
10.106.116.157 ssh_short_name=host1
10.106.116.139 ssh_short_name=host2
```

**Variables**
populate the *ssh_config* key.
```
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
      - line: "testline"
        state: present
      - line: 'export SSH_AUTH_SOCK="${HOME}/.gnupg/S.gpg-agent.ssh"'
        state: present
      - line: "alias ls='ls lah'"
        state: present
    ssh_config:
      - line: "ServerAliveInterval: 10"
      - line: "Compression no"
```

**Result:**
```
# BEGIN ANSIBLE MANAGED BLOCK
Host host1
    Hostname 10.106.116.157
    RemoteForward /home/remember/.gnupg/S.gpg-agent $HOME/.gnupg/S.gpg-agent
    RemoteForward /home/remember/.gnupg/S.gpg-agent.ssh $HOME/.gnupg/S.gpg-agent.ssh
    ServerAliveInterval 10
Host host2
    Hostname 10.106.116.139
    RemoteForward /home/remember/.gnupg/S.gpg-agent $HOME/.gnupg/S.gpg-agent
    RemoteForward /home/remember/.gnupg/S.gpg-agent.ssh $HOME/.gnupg/S.gpg-agent.ssh
    ServerAliveInterval 10
# END ANSIBLE MANAGED BLOCK

```

**Break down**

The host identifier is populated with the `ssh_short_name` host variable.
```
Host host1
```

The `Hostname` is populated with the `inventory_hostname` variable
```
Hostname 10.106.116.139
```

These lines are added by default:
```
RemoteForward /home/remember/.gnupg/S.gpg-agent $HOME/.gnupg/S.gpg-agent
RemoteForward /home/remember/.gnupg/S.gpg-agent.ssh $HOME/.gnupg/S.gpg-agent.ssh
```

Everything below this is populated with the key/values defined in the
`ssh_config` list of the `users` variable

```
ServerAliveInterval 10
```


