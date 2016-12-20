### Prerequisites

Install roles via `ansible-galaxy install -r requirements.yml` from this directory

### About the playbook

`provision-server.yml` is a playbook which will do basic provisioning and hardening tasks for a server. But you can comment out any of the individual `- include: ` lines and run portions of the playbook as well. This was developed/tested on Ubuntu 16.04, but large portions of the playbook should run on CentOS systems as well.

### Running

1. Copy `example.provision-server.vars.yml` to `provision-server.vars.yml` and fill in the relevant variables.
2. Copy `hosts.example` to `hosts` and fill in the IP address(es) of the servers you want to run the script on, along with the `ansible_become_pass` plaintext value.
3. Run `ansible-playbook provision-server.yml -i hosts`. You can also add a `-v` or `-vvv` flag to get more verbose output. Use `--ask-pass` on Linode boxes to get a prompt to provide the root password.

### More about the hosts file

Ansible runs on "hosts" which are defined in a hosts file. The host file specifies which IP addresses ansible will connect to, which user it will use, and the sudo password for each host. You can either use a global hosts file, or specify a hosts file in this directory using the `-i` flag to `ansible-playbook`, e.g. `ansible-playbook -i hosts`. The hosts file (same syntax as a `.ini` file, for what it's worth) should look like this:

`[name-of-hosts-group]` (e.g. test, staging, production), followed by a set of lines containing
`IP Address variable1=value variable2=value`, etc

In order to keep certain secrets out of version control, the `provision-server.yml` playbook depends on the following host-specific variables:

* ansible_become_pass -- Plain-text version of the hashed password which is set in `provision-server.vars.yml`, used when ansible needs to sudo when logged in as the non-root user which is created in the `set-up-users.yml` step.
* ansible_become_user -- Should almost always be root.

So the full host specification would look like
```
[group-name]
i.2.3.4 ansible_become_pass=some-password ansible_become_user=root
```

You'll need to edit the `hosts: ` line in `provision-server.yml` to the name of the hosts group you specify in your local `hosts` file and which you want to edit.

You need ssh key permission to log into the server either as root or as the `user` specified in `provision-server.vars.yml` in order to run the scripts for the first time.
