# Born2beroot
## Todo
### Mandatory part
- [x] install Debian
- [x] running AppArmor `aa-status`
- [x] 2 or more encripted partitions by LVM `lsblk`
- [ ] know the differences between aptitude/apt, what SELinux/AppArmor is
- [x] [SSH only on 4242, no root connection](#ssh)
- [ ] know how to make new account via SSH (Secure Shell)
- [x] [configure with UFW, only 4242 open](#ufw-firewall)
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
    - [ ] 3 attempt for incorrect passwd
    - [ ] custom message when wrong passwd
    - [ ] each sudo action logs in /var/log/sudo/
    - [ ] TTY mode enabled
    - [ ] path used with sudo restricted to /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
- [x] create user saleshin
    - [x] [saleshin in groups user42 and sudo](#work-with-groups)
    - [x] [create user on eval](#create-new-usergroup)
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
### Bonus part
- [ ] correct partitions
- [ ] setup WordPress with next services:
    - [ ] lighttpd
    - [ ] MariaDB
    - [ ] PHP
- [ ] set up service of my choice (LiteSpeed)
- [ ] open extra ports for services with UFW
### Evaluation
- [ ] signature.txt in root of git repo
- [ ] prevent VM from signature changing copy it or use save state
- [ ] commands:
    - [ ] head -n 2 /etc/os-release
    - [ ] /usr/sbin/aa-status
    - [ ] ss -tunlp
    - [ ] /usr/sbin/ufw status
# How to do
## Work with sudo
### Install sudo
su - [login as root. su — switch user]
apt-get update [update list of packages opt -y 'yes' for the questions]
apt-get upgrade
apt install sudo
sudo reboot
sudo -V [sudo version under root user]
### Create new user/group
sudo adduser <login>
sudo addgroup user42 [GID is Group ID]
getent group <groupname> [check group]
### work with groups
sudo adduser <user> <groupname>
getent group sudo [get list of sudoers]
### make sudoer
/etc/sudoers [file with list of users, can change with] visudo
touch /etc/sudoers.d/sudo_config
usermod -aG sudo your_username [put user to sudoer]
sudo visudo [open sudoers file]
[add line] your_username ALL=(ALL) ALL [in section] # User privilege specification
### sudo polices
- touch /etc/sudoers.d/sudo_config [make config file]
- mkdir /var/log/sudo [make log file]
- vim /etc/sudoers.d/sudo_config
- `Defaults  passwd_tries=3
Defaults  badpass_message="Mensaje de error personalizado"
Defaults  logfile="/var/log/sudo/sudo_config"
Defaults  log_input, log_output
Defaults  iolog_dir="/var/log/sudo"
Defaults  requiretty
Defaults  secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"`
## git, vim
apt-get install git -y [and vim]
git --version [and vim]
## SSH
[sudo] apt install openssh-server
sudo systemctl status ssh [check SSH Server Status or]
sudo service ssh status
sudo vim /etc/ssh/sshd_config [under root]
[change line] #Port22 -> Port 4242
[change line] #PermitRootLogin prohibit-password -> PermitRootLogin no
sudo grep Port /etc/ssh/sshd_config [check the port settings]
sudo service ssh restart
sudo service ssh status
### to check port
apt-get install net-tools
netstat -tulpn | grep ssh
### connect
ssh your_username@127.0.0.1 -p 4242
rm ~/.ssh/known_hosts [if problem]
exit
## UFW firewall
sudo apt install ufw
sudo ufw enable
sudo ufw allow 4242
sudo ufw status [numbered]
sudo ufw delete NUM
## Setting Password Policy
- sudo apt-get install libpam-pwquality [install Password Quality Checking Library]
- sudo vim /etc/pam.d/common-password
- [add to line] password requisite pam_deny.so
`minlen=10 ucredit=-1 dcredit=-1 lcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root`
- sudo vim /etc/login.defs
- `PASS_MAX_DAYS 30 and PASS_MIN_DAYS 2 keep PASS_WARN_AGE 7`
- sudo reboot
sudo chage -l username [check policy]
## Script
/usr/local/bin/monitoring.sh
### Architecture
uname -a [all]
### Physical Cores
grep "physical id" /proc/cpuinfo | wc -l
- /proc/cpuinfo file
- wc word count
- -l lines
### Virtual Cores
grep processor /proc/cpuinfo | wc -l
### RAM
free --mega | awk '$1 == "Mem:" {print $3}'
- awk works with rows/columns, has condition
free --mega | awk '$1 == "Mem:" {printf("%.2f"), $3/$2*100}'
### DISK
df -m | grep "/dev/" | grep -v "/boot" | awk '{disk_t += $2} END {printf ("%.1fGb\n"), disk_t/1024}'
- df disk filesystem
- m megabites
- -v exclude
### CPU LOAD
vmstat 1 2 | tail -1 | awk '{printf $15}'
- 1 2 from 1 to 2 sec
- tail -1 last line
cpu_op=$(expr 100 - $cpul)
- expr expression
cpu_fin=$(printf "%.1f" $cpu_op)
### LAST BOOT
who -b | awk '$1 == "system" {print $3 " " $4}'
- -b boot time
### LVM USE
lvmu=$(if [ $(lsblk | grep "lvm" | wc -l) -gt 0 ]; then echo yes; else echo no; fi)
- LVM Logical Volume Manager
- -gt greate then
- fi end of if
### TCP CONNEXIONS
ss -ta | grep ESTAB | wc -l
- ss socket statistics
- -t TCP
- -a all (use + listen)
### USER LOG
users | wc -w
- -w counts words
### NETWORK
ip=$(hostname -I)
mac=$(ip link | grep "link/ether" | awk '{print $2}')
- -I IP-address
### SUDO
journalctl _COMM=sudo | grep COMMAND | wc -l
- log analiser
- _COMM=sudo communicator is subo
## Crontab
sudo crontab -u root -e
*/10 * * * * sh /usr/local/bin/monitoring.sh
## Bonus
### Lighttpd web server
sudo ufw allow 80
sudo lighty-enable-mod fastcgi
sudo lighty-enable-mod fastcgi-php
sudo service lighttpd force-reload
## Wordpress CMS
### install
sudo apt install wget zip
- wget to download files from web
- zip to archive
cd /var/www/
sudo wget https://wordpress.org/latest.zip
sudo unzip latest.zip
sudo mv html/ html_old/
sudo mv wordpress/ html
sudo chmod -R 755 html
### config
cd /var/www/html
cp wp-config-sample.php wp-config.php
vim wp-config.php

`define( 'DB_NAME', 'wp_database' );
define( 'DB_USER', 'saleshin' );
define( 'DB_PASSWORD', 'qwerty' );`
localhost [in browser]
## MariaDB database
sudo apt install mariadb-server
sudo mysql_secure_installation
mariadb
CREATE DATABASE wp_database;
SHOW DATABASES;
CREATE USER 'saleshin'@'localhost' IDENTIFIED BY 'qwerty';
GRANT ALL PRIVILEGES ON wp_database.* TO 'saleshin'@'localhost';
FLUSH PRIVILEGES;
exit
## PHP programming language
sudo apt install php-cgi php-mysql
## LiteSpeed web server
sudo apt update
sudo apt upgrade
wget -O - https://repo.litespeed.sh | sudo bash
sudo apt update
apt-get install openlitespeed
sudo /usr/local/lsws/admin/misc/admpass.sh
sudo ufw allow 8088/tcp
sudo ufw allow 7080/tcp
sudo ufw reload
localhost:7080 [in browser]
## Evaluation
shasum Born2beRoot.vdi
diff signature.txt signature1.txt
sudo ufw status
sudo systemctl status ssh
head -n 2 /etc/os-release
getent group sudo
getent group user42
sudo adduser new username
sudo vim /etc/pam.d/common-password
sudo vim /etc/login.defs
sudo groupadd groupname
sudo usermod -aG groupname username
hostnamectl
hostnamectl set-hostname new_hostname
sudo nano /etc/hosts
lsblk
dpkg -l | grep sudo
sudo ufw status numbered
sudo ufw allow port-id
sudo ufw delete rule number
ssh your_user_id@127.0.0.1 -p 4242
