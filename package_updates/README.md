# Update packages on ubuntu and freebsd
:microscope: #1

The Vagrant file being used is a copy from
`https://github.com/stationgroup/vagrant-labs/tree/master/imperialspeculate`.

Two roles are being used: debian-upgrade (an upstream Galaxy role) and
freebsd-upgrade (a small role based on what was proposed in the comments of #1
and extended with proper support for check mode.)

The upgrade process is contained in the playbook os_upgrade.yml, which will
automatically create proper groups for Ubuntu and FreeBSD hosts. If unneeded,
this first play can be left out, and the target hosts: in the second play can be
replaced by the relevant groups you have in the inventory (e.g. ec2 tags.)

A local ansible.cfg is defined, and needed for these scripts to run out of the
box. This implies that all ansible commands must be run from the
`ansible-experiments/package_updates` folder.

A small script `setup-requirements` is provided, that initializes everything,
including doing a vagrant up. It will generate an ssh-config for said vagrant
boxes, download roles from galaxy, and make a base-install for the hosts
(installing python dependencies, ansible itself on ubuntu1, a deploying an ssh
key to all nodes to be used from the vagrant box `ubuntu1`, as
ansible controller machine.)

When deploying and setting up from the machine where vagrant runs, you need to
add some extra arguments:
`--ssh-extra-args "-F ./vagrant-ssh-config" --inventory hosts-vagrant`
to ansible execution. These are not necessary once running ansible from
`ubuntu1`.
