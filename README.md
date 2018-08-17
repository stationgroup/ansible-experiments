# Users and ssh-configAnsible roles
This repo contains 2 roles:

- **users**: Add users and configure `.bashrc` and `authorized_keys`
- **ssh-config**: Configures a user's `~/.ssh/config`

Both roles make use of the same _users_ variable and are created to give users the freedom to add their own configuration outside of Ansible.

Detailed configuration can be found in the README files inside the role's folders.
