#Vagrant Fedora 20 Box

##Install Vagrant Trigger Plugin
`vagrant plugin install vagrant-triggers`

##First Run
```bash
VAGRANT_SSH_USERNAME=$(whoami) VAGRANT_SSH_PRIVATE_KEY=$(cd $HOME ; pwd)/.ssh/id_rsa VAGRANT_SSH_PUBLIC_KEY=$(cd $HOME ; pwd)/.ssh/id_rsa.pub vagrant up
```

##Some Todo'ish Notes
- Need a way to handle git configuration.
- Need a way to handle opening and forwarding of ports. Currently, I opened up the VirtualBox gui and forwarded the ports.
- Add an empty ./mnt or ./mount folder and allow user to specify what, if anything gets mounted there. i.e. ./mount/opt -> vm's /opt or whatever.
- Config potentially solved by using foreman and a .env file - need to investigate more.
- Multiple boxes on the same host fight over preset port numbers for SSH. Need to figure out how to handle this better. Might be solved already via Ansible's host inventory stuff.
- Update known_hosts on host machine so that subsequent connections don't fail.(SSH whines when I resume the box.)
