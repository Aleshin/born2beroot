# Born2beroot
## Todo
### Mandatory part
- [x] install Debian
- [ ] running AppArmor
- [ ] 2 or more encripted partitions by LVM
- [ ] know the differences between aptitude/apt, what SELinux/AppArmor is
- [ ] SSH only on 4242, no root connection
- [ ] know how to make new account via SSH
- [ ] configure with UFW, only 4242 open
- [ ] firewall active when start VM
- [ ] hostname should be saleshin42, change it on evaluation
- [ ] strong password policy
    - [ ] expire every 30 days
    - [ ] minimum days before modification is 2
    - [ ] worning message 7 days before
    - [ ] min 10 chars long
    - [ ] uppercase, lowercase, number. Not more then 3 same chars
    - [ ] no name in passwd
    - [ ] min 7 chars not included in former pass (not for root passwd)
    - [ ] change all passwds after setting configuration files
- [ ] install and config sudo with strict rules
    - [ ] 3 attempt for incorrect masswd
    - [ ] custom message when wrong passwd
    - [ ] each sudo action logs in /var/log/sudo/
    - [ ] TTY mode enabled
    - [ ] path used with sudo restricted to /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
- [ ] create user saleshin
    - [ ] saleshin in groups user42 and sudo
    - [ ] create user on eval
- [ ] make script monitoring.sh in bash
    - [ ] display info every 10 min (wall). No error must be visible
        - [ ] The architecture of your operating system and its kernel version.
        - [ ] The number of physical processors.
        - [ ] The number of virtual processors.
        - [ ] The current available RAM on your server and its utilization rate as a percentage.
        - [ ] The current available memory on your server and its utilization rate as a percentage.
        - [ ] The current utilization rate of your processors as a percentage.
        - [ ] The date and time of the last reboot.
        - [ ] Whether LVM is active or not.
        - [ ] The number of active connections.
        - [ ] The number of users using the server.
        - [ ] The IPv4 address of your server and its MAC (Media Access Control) address.
        - [ ] The number of commands executed with the sudo program.
    - [ ] explain how it works
    - [ ] interrupt it without modifying (cron)
- [ ] commands:
    - [ ] head -n 2 /etc/os-release
    - [ ] /usr/sbin/aa-status
    - [ ] ss -tunlp
    - [ ] /usr/sbin/ufw status
### Bonus part
- [ ] correct partitions
- [ ] setup WordPress with next services:
    - [ ] lighttpd
    - [ ] MariaDB
    - [ ] PHP
- [ ] set up service of my choice
- [ ] open extra ports for services with UFW
### Evaluation
- [ ] signature.txt in root of git repo
- [ ] prevent VM from signature changing copy it or use save state
## Work with sudo
su - [login as root. su — switch user]
apt-get update [update list of packages opt -y 'yes' for the questions]
apt-get upgrade
apt install sudo
/etc/sudoers [file with list of users, can change with] visudo
getent group sudo [get list of sudoers]
usermod -aG sudo your_username [put user to sudoer]
sudo visudo [open sudoers file]
[add line] your_username ALL=(ALL) ALL [in section] # User privilege specification