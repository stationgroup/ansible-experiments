# Ansible playbook to remove oldest AMI and attached snapshots

Tested with Ansible version: 2.5.4

## Requirements
This playbook needs the Python AWS SDK installed (both boto & boto3)

```
sudo pip install boto boto3
# or
pip install --user boto boto3
# or use pipenv for a virtualenv setup..
```


## Variables
- enable_debug: True / False (prints some info about registered variables)
- aws_profile: the AWS profile name
- log_destination: path to the file where actions will be logged.
- ami_tag: AMI's with given tag will be filtered out. The oldest is then removed

## AWS authentication
### AWS profiles
In this example we create an aws profile with the name `aws-ansible`.

Create an AWS profile by editing `~/.aws/credentials` and add the following
stanza:
```
[aws-ansible]
aws_access_key_id = ***
aws_secret_access_key = ***
region = eu-west-1
```

Set the Playbook variable `aws_profile` to the name of this profile

example:
```
  vars:
    aws_profile: aws-ansible
    ...
```


## Testing
In the `test` folder of this repository you can find an Ansible playbook that
creates some test AMI's.

- AMI-001 (oldest)
- AMI-002
- AMI-003 (newest)

When running the cleanup playbook AMI-001 should be removed first. On subsequent runs AMI-002 should be removed and then AMI-003.
If no AMI's with the tag `DestroyImage: true` are available. The playbook skips
all tasks.

When AMI's with multiple snapshots are deregistered, all the attached snapshots
will be deleted.

**NOTE:** The deregistration of AMI's takes some time. So subsequent
runs of the cleanup playbook short after each other will result in the playbook
trying to deregister and delete snapshots of the same AMI. This should be an
idempotent action.

This is not the case if AMI's are tagged

## Cleanup Playbook
### prerequisites
The Playbook requires that you set the `AWS_REGION` environment variable because
some modules used in this playbook require this even if it's set in the aws
profile.

Example:
```
 export AWS_REGION=eu-west-1
```

### Run the Playbook
```
$ ansible-playbook playbook.yaml

PLAY [Remove the oldest AMI and associated snapshots] **************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************
ok: [localhost]

TASK [Register the AWS_REGION environment variable.] ***************************************************************************************
ok: [localhost]

TASK [Fail if the AWS_REGION environment var is not set] ***********************************************************************************
skipping: [localhost]

TASK [Gather facts about all AMIs with given tag ] *****************************************************************************************
ok: [localhost]

TASK [Check if log file already exists] ****************************************************************************************************
ok: [localhost]

TASK [Create log file] *********************************************************************************************************************
skipping: [localhost]

TASK [debug] *******************************************************************************************************************************
skipping: [localhost]

TASK [oldest ami] **************************************************************************************************************************
ok: [localhost]

TASK [debug] *******************************************************************************************************************************
skipping: [localhost]

TASK [Deregister AMI.] *********************************************************************************************************************
changed: [localhost]

TASK [LOG action] **************************************************************************************************************************
changed: [localhost]

TASK [Cleanup AMI snapshots] ***************************************************************************************************************
changed: [localhost] => (item={u'ebs': {u'encrypted': False, u'snapshot_id': u'snap-0b4d8ef6c1bc098d1', u'delete_on_termination': True, u'volume_type': u'gp2', u'volume_size': 8}, u'device_name': u'/dev/xvda'})
changed: [localhost] => (item={u'ebs': {u'encrypted': False, u'snapshot_id': u'snap-09c25bbe838912ced', u'delete_on_termination': True, u'volume_type': u'standard', u'volume_size': 8}, u'device_name': u'/dev/sdb'})

TASK [LOG action] **************************************************************************************************************************
changed: [localhost] => (item={u'ebs': {u'encrypted': False, u'snapshot_id': u'snap-0b4d8ef6c1bc098d1', u'delete_on_termination': True, u'volume_type': u'gp2', u'volume_size': 8}, u'device_name': u'/dev/xvda'})
changed: [localhost] => (item={u'ebs': {u'encrypted': False, u'snapshot_id': u'snap-09c25bbe838912ced', u'delete_on_termination': True, u'volume_type': u'standard', u'volume_size': 8}, u'device_name': u'/dev/sdb'})

PLAY RECAP *********************************************************************************************************************************
localhost                  : ok=9    changed=4    unreachable=0    failed=0
```

### Logging
Info about the cleanup actions are kept in a log file (default:
/tmp/ansible-ami-cleanup.log)

```
2018-06-09T07:52:44Z AMI CLEANUP - Deregistered AMI: ami-577e7d2e
2018-06-09T07:52:44Z AMI CLEANUP - Removed snapshots: snap-00c90dc0cfa23ae75
2018-06-09T07:52:44Z AMI CLEANUP - Removed snapshots: snap-0ebfc8f7defefc968
2018-06-09T08:10:58Z AMI CLEANUP - Deregistered AMI: ami-f77f7c8e
2018-06-09T08:10:58Z AMI CLEANUP - Removed snapshots: snap-0b4d8ef6c1bc098d1
2018-06-09T08:10:58Z AMI CLEANUP - Removed snapshots: snap-09c25bbe838912ced
```

## Contributors

Thanks goes to these wonderful people:

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
| [<img src="https://avatars2.githubusercontent.com/u/382239" width="100px;"/><br /><sub>Serge van Ginderachter</sub>](https://github.com/srgvg)<br />[📖](https://github.com/stationgroup/ansible-experiments/commits?author=srgvg) | [<img src="https://avatars1.githubusercontent.com/u/676958" width="100px;"/><br /><sub>Vincent Van der Kussen</sub>](https://github.com/vincentvdk)<br />[📖](https://github.com/stationgroup/ansible-experiments/commits?author=vincentvdk) | [<img src="https://avatars.githubusercontent.com/u/5644977?v=3" width="100px;"/><br /><sub>Bryan Black</sub>](https://bringyourwallet.com)<br />[📖](https://github.com/stationgroup/ansible-experiments/commits?author=reelsense) 
| :---: | :---: | :---: |

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

### **[Support Now](https://bringyourwallet.com/donate)**


## Community Discussion

Text and voice chat on the public mumble server.

**Join the Public Mumble**

Server: `pub.bringyourwallet.com`

Port: `64738`
