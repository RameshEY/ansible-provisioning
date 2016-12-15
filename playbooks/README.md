### Prerequisites

Install roles via `ansible-galaxy install -r requirements.yml` from this directory

### Using the ansible playbook

`provision-server.yml` is a playbook which will cover the whole stack of provisioning a new server for the HPTN site on Drupal 8, Ubuntu 16.04. But you can comment out any of the individual `- include: ` lines and run portions of the playbook as well.

Ansible runs on "hosts" which are defined in a hosts file. You can either use a global hosts file, or specify a hosts file in this directory using the `-i` flag to `ansible-playbook`, e.g. `ansible-playbook -i hosts`. The hosts file (same syntax as a `.ini` file, for what it's worth) should look like this:

`[name-of-hosts-group]` (e.g. test, staging, production), followed by a set of lines containing
`ip-address variable1_name=variable1_values variable2_name=variable2_value`, etc

In order to keep certain secrets out of version control, the `provision-server.yml` playbook depends on the following host-specific variables:

* ansible_become_pass -- Plain-text version of the hashed password which is set in `provision-server.vars.yml`, used when ansible needs to sudo
* aws_access_key_id -- Access key for AWS S3
* aws_secret_access_key -- Access key secret for AWS S3

So the full host specification would look like
```
[group-name]
i.2.3.4 ansible_become_pass=some-password aws_access_key_id=key-id aws_secret_access_key=secret
```

You'll need to edit the `hosts: ` line in `provision-server.yml` to the name of the hosts group you specify in your local `hosts` file and which you want to edit.

Further, you'll need to edit the `remote_user: ` line to a github username which you have the private key installed locally for (so that you can SSH in when that public key is defined). This username must also be in the `sudo_users:` defined in `provision-server.vars.yml`.

Lastly, you need ssh key permission to log into the server as root in order to run the scripts for the first time.
