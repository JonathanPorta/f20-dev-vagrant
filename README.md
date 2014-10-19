## Vagrant Fedora 20 Box

### Install Vagrant Trigger Plugin
`vagrant plugin install vagrant-triggers`

### Uh, What is this?
I wanted to be able to create a clean VM for each project that I was working on. I wanted new VM's to be easily created, and be constrained to live in a single directory.

### Usage
This repository is designed to be used by making a new clone for each new Vagrant box.
It is designed to be helpful by caching your credentials to a `.vagrant-user` file in the root of the repository.
The first time you spinup the VM you need to specify your `username`, `public key path`, and `private key path`.(Your private key will be used by Vagrant to connect to your VM, it won't be sent anywhere.)
Afterwards, you will be able to use `vagrant up` and the other vagrant commands as normal.

#### First Time
```bash
VAGRANT_SSH_USERNAME=$(whoami) VAGRANT_SSH_PRIVATE_KEY=$(cd $HOME ; pwd)/.ssh/id_rsa VAGRANT_SSH_PUBLIC_KEY=$(cd $HOME ; pwd)/.ssh/id_rsa.pub vagrant up
```
#### Subsequently
```bash
vagrant up
```

### Digital Ocean Spinup
First, install the Digital Ocean plugin:
`vagrant plugin install vagrant-digitalocean`

#### First Time:
```bash
VAGRANT_SSH_PRIVATE_KEY=$(cd $HOME ; pwd)/.ssh/digitalocean_rsa VAGRANT_SSH_PUBLIC_KEY=$(cd $HOME ; pwd)/.ssh/digitalocean_rsa.pub VAGRANT_DO_TOKEN=YOUR_TOKEN_HERE vagrant up --provider=digital_ocean
```
#### Subsequently
```bash
vagrant up
```

Easy, huh?

### Some Todo'ish Notes
- Need a way to handle git configuration.
- Need a way to handle opening and forwarding of ports. Currently, I opened up the VirtualBox gui and forwarded the ports.
- Add an empty ./mnt or ./mount folder and allow user to specify what, if anything gets mounted there. i.e. ./mount/opt -> vm's /opt or whatever.
- Config potentially solved by using foreman and a .env file - need to investigate more.
- Multiple boxes on the same host fight over preset port numbers for SSH. Need to figure out how to handle this better. Might be solved already via Ansible's host inventory stuff.
- Update known_hosts on host machine so that subsequent connections don't fail.(SSH whines when I resume the box.)
