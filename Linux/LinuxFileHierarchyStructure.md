# Linux file hierarchy structure

In Linux/Unix operating system everything is a file even directories are files, files are files,
and devices like mouse, keyboard, printer, etc are also files.
Here we are going to see the Directory Structure in Linux.

![linux file hierarchy](linux-dir.jpg "linux file hierarchy")

### 1. / (Root):

**Primary hierarchy root and root directory of the entire file system hierarchy.**

* Every single file and directory start from the root directory.
* The only root user has the right to write under this directory.
* /root is the root user’s home directory, which is not the same as /

### 2. /bin :

**Essential command binaries that need to be available in single-user mode; for all users, e.g., cat, ls, cp.**

* Contains binary executables.
* Common linux commands you need to use in single-user modes are located under this directory.
* Commands used by all the users of the system are located here e.g. ps, ls, ping, grep, cp

### 3. /boot :

**Boot loader files, e.g., kernels, initrd.**

### 4. /dev :

**Essential device files, e.g., /dev/null.**

* These include terminal devices, usb, or any device attached to the system.
* Example: /dev/tty1, /dev/usbmon0

### 5. /etc :

**Host-specific system-wide configuration files.**

* Contains configuration files required by all programs.
* This also contains startup and shutdown shell scripts used to start/stop individual programs.
* Example: /etc/resolv.conf, /etc/logrotate.conf.

### 6. /home :

**Users’ home directories, containing saved files, personal settings, etc.**

* Home directories for all users to store their personal files.
* example: /home/kishlay, /home/kv

### 7. /lib:

**Libraries essential for the binaries in /bin/ and /sbin/.**

* Library filenames are either ld* or lib*.so.*
* Example: ld-2.11.1.so, libncurses.so.5.7

### 8. /media:

**Mount points for removable media such as CD-ROMs (appeared in FHS-2.3).**

* Temporary mount directory for removable devices.
* Examples, /media/cdrom for CD-ROM; /media/floppy for floppy drives; /media/cdrecorder for CD writer

### 9. /mnt :

**Temporarily mounted filesystems.**

* Temporary mount directory where sysadmins can mount filesystems.

### 10. /opt :

**Optional application software packages.**

* Contains add-on applications from individual vendors.
* Add-on applications should be installed under either /opt/ or /opt/ sub-directory.

### 11. /sbin :

**Essential system binaries, e.g., fsck, init, route.**

* Just like /bin, /sbin also contains binary executables.
* The linux commands located under this directory are used typically by system administrators, for system maintenance
  purposes.
* Example: iptables, reboot, fdisk, ifconfig, swapon

### 12. /srv :

**Site-specific data served by this system, such as data and scripts for web servers, data offered by FTP servers, and
repositories for version control systems.**

* srv stands for service.
* Contains server specific services related data.
* Example, /srv/cvs contains CVS related data.

### 13. /tmp :

**Temporary files. Often not preserved between system reboots and may be severely size restricted.**

* Directory that contains temporary files created by system and users.
* Files under this directory are deleted when the system is rebooted.

### 14. /usr :

**Secondary hierarchy for read-only user data; contains the majority of (multi-)user utilities and applications.**

* Contains binaries, libraries, documentation, and source-code for second level programs.
* /usr/bin contains binary files for user programs. If you can’t find a user binary under /bin, look under /usr/bin. For
  example: at, awk, cc, less, scp
* /usr/sbin contains binary files for system administrators. If you can’t find a system binary under /sbin, look under
  /usr/sbin. For example: atd, cron, sshd, useradd, userdel
* /usr/lib contains libraries for /usr/bin and /usr/sbin
* /usr/local contains user’s programs that you install from source. For example, when you install apache from source, it
  goes under /usr/local/apache2
* /usr/src holds the Linux kernel sources, header-files and documentation.

### 15. /proc:

**Virtual filesystem providing process and kernel information as files. In Linux, it corresponds to a procs mount.
Generally, automatically generated and populated by the system, on the fly.**

* Contains information about system process.
* This is a pseudo filesystem that contains information about running processes. For example: /proc/{pid} directory
  contains information about the process with that particular pid.
* This is a virtual filesystem with text information about system resources. For example: /proc/uptime

[based on](https://www.geeksforgeeks.org/linux-file-hierarchy-structure/)

# System Configuration Files:

| Configuration Files | Description |
|---    |---|
| /etc/bashrc | It is used by bash shell that contains system defaults and aliases. |
| /etc/crontab | A shell script to run specified commands on a predefined time interval. |
| /etc/exports | It contains information on the file system available on the network. |
| /etc/fstab | Information of the Disk Drive and their mount point. |
| /etc/group | It is a text file to define Information of Security Group. |
| /etc/grub.conf | It is the grub bootloader configuration file. |
| /etc/init.d | Service startup Script. |
| /etc/lilo.conf | It contains lilo bootloader configuration file. |
| /etc/hosts | Information of IP and corresponding hostnames |
| /etc/hosts.allow | It contains a list of hosts allowed accessing services on the local machine. |
| /etc/host.deny | List of hosts denied accessing services on the local machine. |
| /etc/inittab | INIT process and their interaction at the various run levels. |
| /etc/issue | Allows editing the pre-login message. |
| /etc/modules.conf | It contains the configuration files for the system modules. |
| /etc/motd | It contains the message of the day. |
| /etc/mtab | Currently mounted blocks information. |
| /etc/passwd | It contains username, password of the system, users in a shadow file. |
| /etc/printcap | It contains printer Information. |
| /etc/profile | Bash shell defaults. |
| /etc/profile.d | It contains other scripts like application scripts, executed after login. |
| /etc/rc.d | It avoids script duplication. |
| /etc/rc.d/init.d | Run Level Initialisation Script. |
| /etc/resolv.conf | DNS being used by System. |
| /etc/security | It contains the name of terminals where root login is possible. |
| /etc/skel | Script that initiates new user home directory. |
| /etc/termcap | An ASCII file that defines the behavior of different types of the terminal. |
| /etc/X11 | Directory tree contains all the conf files for the X-window System. |

| Virtual and Pseudo Process Related Files | Description |
|---    |---|
| /proc/cpuinfo | CPU Information |
| /proc/filesystems | It keeps useful info about the processes that are currently running. |
| /proc/interrupts | it keeps the information about the number of interrupts per IRQ. |
| /proc/ioports | Contains all the Input and Output addresses used by devices on the server |
| /proc/meminfo | It reports the memory usage information. |
| /proc/modules | Currently using kernel module. |
| /proc/mount | Mounted File-system Information. |
| /proc/stat | It displays the detailed statistics of the current system. |
| /proc/swaps | It contains swap file information. |

| Log Files | Description |
|---    |---|
| /var/log/lastlog	 | It stores user’s last login info. |
| /var/log/syslog	 | Shows general messages and info regarding the system. Basically a data log of all activity throughout the global system. |

[based on](https://www.geeksforgeeks.org/linux-directory-structure/)	
	 
	
	
	
	
	
	 