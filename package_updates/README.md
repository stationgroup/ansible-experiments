# Update packages on Ubuntu and Freebsd

![](https://cdn.ipfu.org/git/assets/gif/shell-ansible-experiments.gif)

## Summary

* Updates _Ubuntu_ packages and OS with `apt`.

* Updates _FreeBSD_ packages with `pkg`, and OS with `freebsd-update`.

* **Updates [_all-the-things_](https://www.google.com/search?q=all+the+things), all at once.**

## :book: Getting Started

## :white_check_mark: Dependencies

### macOS Dependencies

```bash
brew cask install vagrant virtualbox ; brew install git ansible
```

### Linux - Ubuntu Dependencies

Add this Ansible PPA and [Unofficial Vagrant Debian Repository](https://vagrant-deb.linestarve.com/) because [Vagrant is made by the excuse making developers at HashiCorp](https://github.com/hashicorp/vagrant-installers/issues/12#issuecomment-328379740).

_If you use the Ubuntu Repository version of Vagrant, it is **very** behind release and will likely throw errors._

```bash
sudo add-apt-repository ppa:ansible/ansible ; sudo apt-get update ; sudo bash -c 'echo deb https://vagrant-deb.linestarve.com/ any main > /etc/apt/sources.list.d/wolfgang42-vagrant.list' && sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key AD319E0F7CFFA38B4D9F6E55CE3F3DE92099F7A4 && sudo apt-get update
```

Then install the dependencies.

```bash
sudo apt install -y vagrant virtualbox ansible git rsync python-pip
```

_If you feel you **need** the latest VirtualBox you can [follow the Oracle instructions](https://www.virtualbox.org/wiki/Linux_Downloads) any time._

### FreeBSD Dependencies

`root:#`
```bash
pkg install --yes vagrant virtualbox-ose-nox11 ansible git rsync ca_root_nss
```

**Windows (TBD) Dependencies**

# :floppy_disk: Install and Usage

One-liner to start and provision all the VMs and prime them for _Ansible_.

```bash
cd ; git clone https://github.com/stationgroup/ansible-experiments ; cd ~/ansible-experiments/package_updates ; ./setup-requirements
```

Now while you're in the directory `ansible-experiments/package_updates`, you can ssh from the _host_ to your _Ansible_ control node, `ubuntu1`:

```bash
vagrant ssh ubuntu1
```

Or, while you're still at the VM host shell you can check and see if your VMs are running:

```bash
vagrant status
```

If you're all done, need disk space, or you fucked up, you can `destroy` everything or `halt` it.

_E.g._ `vagrant destroy` `vagrant halt` `vagrant up` [`RTFM`](https://www.vagrantup.com/docs/cli/up.html)

![](https://cdn.ipfu.org/git/assets/gif/shell-ansible-experiments-long.gif)

## :neckbeard: Details

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

## Contributors

Thanks goes to these wonderful people:

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
| [<img src="https://avatars2.githubusercontent.com/u/382239" width="100px;"/><br /><sub>Serge van Ginderachter</sub>](https://github.com/srgvg)<br />[📖](https://github.com/stationgroup/ansible-experiments/commits?author=srgvg) | [<img src="https://avatars.githubusercontent.com/u/5644977?v=3" width="100px;"/><br /><sub>Bryan Black</sub>](https://bringyourwallet.com)<br />[📖](https://github.com/stationgroup/ansible-experiments/commits?author=reelsense) |
| :---: | :---: |

<!-- ALL-CONTRIBUTORS-LIST:END -->

Contributions of any kind are welcome!


# Top Supporters

Monthly supporters only.

Amount     | Supporter Name            | Supporter Domain       
-----------|---------------------------|------------------------
$50/month  | [Tugger Hosting][thgh]    | [tuggerhosting.com][th]

[reelsense]: https://github.com/reelsense
[byw]: http://frothymix.info
[thgh]: https://github.com/TuggerHosting
[th]: https://tuggerhosting.com/
[ydn]: https://yelladognetworks.com


## Support Development

### **[Support Now](https://reelsense.tv/donate)**


## Community Discussion

Text and voice chat on the public mumble server.

**Join the Public Mumble**

Server: `pub.bringyourwallet.com`

Port: `64738`

