
This is an Ansible playbook to create and bootstrap a brand new DigitalOcean virtual server. 

Tested With Ubuntu LTS 12.04 32bit

# Setup
just run `sudo pip install -r requirements.txt` and following packages will be installed 
```
ansible 1.6 (git)
dopy python module
```
Ansible 1.6 is required for this playbook, `ufw` and `debconf` modules not available in stable version yet

# Initial configuration
You should know you `Client ID` and `API key` from you Digital Ocean [account](https://cloud.digitalocean.com/droplets)

All configurable variables are in `vars.yml` file.
Those variables are required
* DigitalOcean `Client ID`
* DigitalOcean `API key`
* ssh public key file

# Run! 
```
ansible-playbook -i localhost newdroplet.yml
```
Be aware playbook will change root password you received by email. SSH login for the root user will be disabled as well
so use admin user account and `sudo` instead. All new passwords will be saved into ./credentials/$hostname/$username files.


# Playbook is actually doing steps

## Droplet 

* deploy ssh keys 
* create droplet

## Users and groups 
* configure remote access 
  * change default root password - always a good idea, default root password received by email in clear text
  * create admin user profile - it would be a main profile for all operations 

## Security settings
* configure sudo -- allow sudo for certain groups of users (administrators)
* configure ssh 
  * create new server ssh keys -- Despite the fact that [this](https://www.digitalocean.com/company/blog/avoid-duplicate-ssh-host-keys/) is not an issue anymore it seems like good practice for me. The default keys have been created out of my control.
  * restrict sshd settings
    * Restrict ssh login for certain group of users (`AllowGroup`) 
    * Disable root login via ssh (`RootLogin no`) 
    * Password guessing protection (`MaxAuthTries`, `LoginGraceTime`, `MaxSessions`, `MaxStartups`)
* enable `ufw` (firewall)

## Software 

* install updates -- it goes without saying 
* configure mail forwarding for admin user -- to keep in touch with my servers 
* fail2ban -- prevent password guessing 
* ntpd is not about security however it's a vps so this thing is essential

# Next steps to enhance security (not implemented)
* Restrict ssh access by IP/network -- this is really good idea for production environment 
* Change default ssh port or enable port knocking -- mitigate massive port scan attempts
* disable ipv6
* There are some options /etc/sysctl.conf (coredums and etc)
* enable mount options for /tmp (noexec, nodev) 