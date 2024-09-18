# Enumeration

Enumeration is the key for privilege escalation several helper scripts are found on [LinEnum](https://github.com/rebootuser/LinEnum) can assist with the enumeration process .

## Key factors to Enumerate
#### 1. OsVersion :- 
	knowing the distro being used will give you an idea of tools might be available as well as searching for public exploits for that OS version.
### 2. Kernel_Version:-
	As with OS knowing the kernel version might lead to publicly known exploits , Kernl explots , However , May Lead to system instability so make sure you fully understand the exploit and its possible ramifications.
### 3. Running_Services:-
	knowing what services are running on the host espicially as root can be an easy path to privilege escalation e.g. A miscofigured or vulnerable service.
```
ps aux | grep root #list all cuurent root processes
```
### 4. Installed packages and versions:-
	similar to services out-of-date and vulnerable packages may lead to privilege escalation e.g. screen v4.05.00 suufers from a privilege escalation vulnerability that may lead to higher privileges.
### 5. Logged_in_users:-
	knowing which usaers are logged into the system can give more vectors to lateral movement and privilege escalation.
### 6. User_Home_Dir:-
	are other users home dirs accessible, they may also contain SSH keys used to acces other system or config. , files with credentials taht may be used for lateral movement or gaining entry to Active Directory.
### 7. SSH_Dir_contents:-
	If you find an ssh key for your current user it could be used to gain access on other systems within the network. 
```
ls -l ~/.ssh #List contents of the ssh directory
```
		
### 8. Bash_History 
	it is important to check the bash history as there is a chance that password are passed as argument in plain text
### 9. Sudo_Privileges:-
	is there commands that can be run as other user's or as __root__ , It often also includes __NOPASSWD__ so the user can run the specified command without needing to have the user's credentials
```
sudo -l #List commands that canf be run as sudo 
```

### 10. Possible_Visible_Passwords:-
	__Confing_Files__ can hold a wealth of information it is worth searching through all files with a .conf or .config extensions for credentials
	__Shadow_Files__ if the shadow file is readable you'' be able to gather passord hashes for all users who have a password set, while it doesnt guarantee access these hashes maybe cracked usingg tools suchas hashcat
	__/etc/passwd__ occasionaly password hashes may visible in this file which is readable by all users 
	
### 11. Cron_Jobs:-
	cron jobs on linux systems are scheduled tasks mostly used for maintenance and backup tasks in conjuctions with other misconfigurations such as relative paths or weak permissions they may lead to escalated privileges 
```
ls -la /etc/cron.daily #List all cron jobs 
```

### 12. Writeables_Files_and_Dirs:-
	__writable_files__ are any scripts or config files world-writables, if yes , altering them can extremely destructive and any scripts that are as root using cron jobs can be modified slightly to appedn a command
	__writable_directories__if you need to download tools to the system you need to know which dirs are writeable, additionaly you may discover a writeable directory where cron jobs places files providing an idea of how often they run
```
find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null #list writeable dirs 
find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null #list writeable files
```
### 13. Additional:-
	check if binaries are set with __SETUID__ or __SETGID__ if so it can be run by the users without granting root access and may be exploitable
	__File systems and additional drives__ if theres an umnounted files system you may find sensitive information and credentals that can lead to higher privileges
```
lsblk #List all drives
```
---
# Gaining Situational Awareness

For the purposes of this module we'll begin with an Ubuntu target covering general techniques .
##### Basic commands for orientation

+  __whoami__ --- what user we are currently
+  __id__ --- what groups does the cuurent user belong to
+ __hostname__ --- what is the server named (does the name convention lead to sth)
+ __ifconfig__ or __ip -a__ --- what's our subnet does the host have additional NICs 
+ __sudo -l__ --- can our user run anything as root without needing a password
```
cat /etc/os-release # to enumerate host info
```

follow the basic orientation steps we also need to check the user's __PATH__ , if it is misconfigured it may be used to elevate privileges, env variablkes also may contain valuable information.

```
uname -a # to note down the kernel version this could lead to some wellknown public exploits
```

next we need to know what cpu does the host use and what terminals are available to us
```
cat /etc/shells
cat lscpu # list cpu info
```

we should also check the defenses available (most probably will not have such privileges) but anyways look for
- [Exec Shield](https://en.wikipedia.org/wiki/Exec_Shield)
- [iptables](https://linux.die.net/man/8/iptables)
- [AppArmor](https://apparmor.net/)
- [SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux)
- [Fail2ban](https://github.com/fail2ban/fail2ban)
- [Snort](https://www.snort.org/faq/what-is-snort)
- [Uncomplicated Firewall (ufw)](https://wiki.ubuntu.com/UncomplicatedFirewall)

next, to enumerate the block devices and discover if we can mount an additional drive that may contain some credentials, we may also find information about printers in the system  
```
lsblk #info about block devices
lpstat # printer information
cat /etc/fstab # check for credentials on unmounted drives
```

---
## The Challenge 

To do this modules challenge you'll need to be familiar with the HTB flag format , then simply running the following command will give you the flag .
```
grep -r -l 'HTB{' / 2>/dev/null
```

---

# Services & Internals Enumeration

