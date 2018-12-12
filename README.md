# Udacity Full Stack Web Developer Project 6 - Linux Server Configuration
## Udacity Reviewer

Public IP Address: 18.211.87.104

SSH Port: 2200

Account: grader/grader

URL of Hosted Web Application: http://18.211.87.104/ (uses Github authorization)

## Cloud Server

- Amazon Lightsail server 512 MB RAM, 1 vCPU, 20 GB SSD
- Ubuntu 18.04 LTS



## Summary of Configuration

### Lightsail Configuration

### ![lightsail-main](/home/aimeeu/Dev/git/github.com/aimeeu/udacity-fsnd-proj06/docs/lightsail-main.png)

1. Create/attach static IP

2. Configure Lightsail firewall

   ![project6-networking](/home/aimeeu/Dev/git/github.com/aimeeu/udacity-fsnd-proj06/docs/project6-networking.png)

## Summary of Software Installed
- aptitude
- ufw
- apache2
- libapache2-mod-wsgi-py3
- python3-dev
- python3-pip
- Udacity Full Stack Web Developer - Project 4 - Item Catalog and required packages

### Server Configuration 

#### Update OS

```
ubuntu@ip-172-26-1-224:~$ sudo apt-get update
ubuntu@ip-172-26-1-224:~$ sudo apt-get dist-upgrade
```
#### Set Local Time to UTC

```
ubuntu@ip-172-26-1-224:~$ sudo timedatectl set-timezone UTC
ubuntu@ip-172-26-1-224:~$ timedatectl
                      Local time: Tue 2018-12-11 17:23:42 UTC
                  Universal time: Tue 2018-12-11 17:23:42 UTC
                        RTC time: Tue 2018-12-11 17:23:43
                       Time zone: UTC (UTC, +0000)
       System clock synchronized: yes
systemd-timesyncd.service active: yes
                 RTC in local TZ: no
```

#### Add Grader User

```
ubuntu@ip-172-26-1-224:~$ sudo adduser grader
Adding user `grader' ...
Adding new group `grader' (1001) ...
Adding new user `grader' (1001) with group `grader' ...
Creating home directory `/home/grader' ...
Copying files from `/etc/skel' ...
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
Changing the user information for grader
Enter the new value, or press ENTER for the default
	Full Name []: Udacity Grader
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
Is the information correct? [Y/n] y
ubuntu@ip-172-26-1-224:~$ sudo usermod -aG sudo grader
```
#### Add SSH Key to Authorized Keys

```
ubuntu@ip-172-26-1-224:~$ cd /home/grader
ubuntu@ip-172-26-1-224:/home/grader$ sudo mkdir .ssh
ubuntu@ip-172-26-1-224:/home/grader$ cd .ssh
ubuntu@ip-172-26-1-224:/home/grader/.ssh$ sudo nano authorized_keys
```
#### Install, Configure, and Enable the Uncomplicated Firewall (ufw)

```
ubuntu@ip-172-26-1-224:~$ sudo aptitude install ufw
ubuntu@ip-172-26-1-224:/home/grader/.ssh$ sudo ufw allow 2200
Rules updated
Rules updated (v6)
ubuntu@ip-172-26-1-224:/home/grader/.ssh$ sudo ufw allow http
Rules updated
Rules updated (v6)
ubuntu@ip-172-26-1-224:/home/grader/.ssh$ sudo ufw allow https
Rules updated
Rules updated (v6)
ubuntu@ip-172-26-1-224:/home/grader/.ssh$ sudo ufw allow 123
Rules updated
Rules updated (v6)
ubuntu@ip-172-26-1-224:~$ sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
ubuntu@ip-172-26-1-224:~$ sudo ufw status
Status: active
```
#### Modify sshd_config

Modify sshd_config to listen on port 2200 and to not allow root access:

AllowUsers ubuntu grader
Port 2200
PermitRootLogin no

```
ubuntu@ip-172-26-1-224:~$ sudo nano /etc/ssh/sshd_config
ubuntu@ip-172-26-1-224:~$ sudo reboot
```
Verify grader ssh access:
```
aimeeu@aimeeu-7520:~$ ssh grader@18.211.87.104 -p 2200
Welcome to Ubuntu 18.04.1 LTS (GNU/Linux 4.15.0-1029-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Dec 12 18:27:45 UTC 2018

  System load:  0.0                Processes:           88
  Usage of /:   10.8% of 19.32GB   Users logged in:     0
  Memory usage: 44%                IP address for eth0: 172.26.1.224
  Swap usage:   0%

 * MicroK8s is Kubernetes in a snap. Made by devs for devs.
   One quick install on a workstation, VM, or appliance.

   - https://bit.ly/microk8s

 * Full K8s GPU support is now available! Get it in MicroK8s, CDK,
   and on GKE with Ubuntu workers.

   - https://blog.ubuntu.com/2018/12/10/using-gpgpus-with-kubernetes


  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

0 packages can be updated.
0 updates are security updates.


Last login: Tue Dec 11 17:20:48 2018 from 99.129.239.198
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
```

#### Install Apache2, WSGI
```
ubuntu@ip-172-26-1-224:~$ sudo apt-get install apache2
ubuntu@ip-172-26-1-224:~$ apachectl -V
Server version: Apache/2.4.29 (Ubuntu)
Server built:   2018-10-10T18:59:25
Server's Module Magic Number: 20120211:68
Server loaded:  APR 1.6.3, APR-UTIL 1.6.1
Compiled using: APR 1.6.3, APR-UTIL 1.6.1
Architecture:   64-bit
Server MPM:     event
  threaded:     yes (fixed thread count)
    forked:     yes (variable process count)
ubuntu@ip-172-26-1-224:~$ sudo apt-get install libapache2-mod-wsgi-py3 python3-dev
ubuntu@ip-172-26-1-224:~$ sudo a2enmod wsgi
ubuntu@ip-172-26-1-224:~$ sudo apache2ctl restart 
ubuntu@ip-172-26-1-224:~$ sudo ufw app list
Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH
ubuntu@ip-172-26-1-224:~$ sudo ufw allow 'Apache Full'
Rules updated
Rules updated (v6)
```