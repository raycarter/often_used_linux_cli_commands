Daily often used commands under Linux (Ubuntu)
==============================================

This is a list of my daily often used commands under Ubuntu Linux server and 
desktop. It's for the people like me who do not have a good memory. I hope it 
will save you a lot of time if you have this list near at hand and mostly don't 
need to search a command online anymore. The comments before every command are 
not trying to explain every option in the command. If you have any question 
about the options please see the **--help** or **man** manuals. 

There are a lot of other tricks to use the commands or combinations of the commands. 
If you want to add your often used commands in the list, please **fork** this 
and make **Pull Request**. I'm still trying to update the list if I have some 
time. 

Enjoy the list!

> Fortunately I have the **root** privileges mostly. So the most of the 
following commands don't have a **sudo** prefix.
 
### User administration

	# create a user with home-dir
	useradd -o -m -s /bin/bash/ username
	
	# create a super user, make him the SAME user as root and have home-dir
	# (BUT BE CAREFUL OF DOING THIS ENLESS YOU KNOW WHAT YOU ARE DOING)
	# to delete this super user: change the uid from 0 to other in the /etc/passwd
	useradd -u 0 -g 0 -o -m -s /bin/bash username 
	
	# change the password of a user	
	passwd username
	
	# delete a user and his home-dir
	userdel -r username  
	
	# add a group (with --system is a system group)
	addgroup groupname  
	
	# add a user to a group
	usermod -a -G groupname username
	
	# delete a user from a group
	gpasswd -d username groupname

	# show log of users' login
	last -a
	
	# show all users' name, id, sh/bash
	cat /etc/passwd
	
	# show all groups' name, id, assigned users
	cat /etc/group

### APT and dpkg stuffs:
	
	# update apt repository data
	apt-get update
	
	# install a package with version number
	apt-get install abc=5.5.29...
	
	# search a package with name
	apt-cache search packagename
	
	# uninstall a package
	apt-get remove packagename
	
	# show information about a package
	apt-cache showpkg packagename
	
	# compare the version of a package in the apt repository and local
	apt-cache policy packagename
	
	# CLI-gui for apt
	aptitude
	
	# show a list of upgradeable packages on the machine
	apt-get -u upgrade
	
	# upgrade only one package
	apt-get install --only-upgrade packagename
	
	# search a package that contains the given file or command on the machine
	dpkg -S file/command
	
	# list all installed packages
	dpkg -l
	
	# search packages that contain the given file name (can be non-installed)
	apt-file search filename 

	# delete downloaded files of installation (to free some spaces on disk)
	apt-get autoclean 

	# PPA install:
	add-apt-repository ppa:user/ppa-name
	apt-get update
	apt-get install packageInThePPA

	# list all packages in one PPA
	grep ^Package /var/lib/apt/lists/ppaname	
	
	### backup installed packages and restore in another machine
	# create a backup of currently installed packages:
	dpkg --get-selections > packagesList
	# restore installations from the list packagesList:
	dpkg --clear-selections
	dpkg --set-selections < packagesList
	apt-get autoremove
	apt-get dselect-upgrade
	
### Port forwarding over SSH: 
	
	# Let's say, I want through local port 3316 to connect 3306 on example.org with username: 
	ssh -f username@example.org -L 3316:localhost:3306 -N

### Ubuntu-Destop running in VirtualBox under Windows:
	
	# auto setup the resolution to the size of the VM-window in the host(Windows)
	
	# in 12.04
	apt-get install virtualbox-ose-guest-utils virtualbox-ose-guest-x11 virtualbox-ose-guest-dkms
	
	# in 14.04
	apt-get install virtualbox-guest-dkms

### Windows-Desktop running in VirtualBox under Ubuntu:

	# auto setup the resolution to the size of the VM-window in the host(Ubuntu)
	# get the name of the running vm
	VBoxManage list runningvms 
	VBoxManage controlvm "name_of_the_running_vm" setvideomodehint 1280 900 32 

### Setup the number of workspaces in Ubuntu : 3*3
	
	dconf write /org/compiz/profiles/unity/plugins/core/hsize 3
	dconf write /org/compiz/profiles/unity/plugins/core/vsize 3
	restart
	
	# actually I don't use the above mentioned 3 commands any more because I found 
	# the unity tweak tool.	
	apt-add-repository ppa:tualatrix/ppa
	apt-get update
	apt-get install ubuntu-tweak
	
### Operations for files or directories:

	# create dir
	mkdir abc
	
	# remove empty dir and its parents (a, b und c will be all deleted)
	rmdir -p a/b/c 
	
	# remove non-empty dir
	rm -fr abc
	
	# rename dir
	mv dirname newdirname
	
	# copy dir (if target exists, copy the file in the target dir)
	cp -r path/to/sourcedir path/to/targetdir
	
	# create file
	touch filename
	
	# copy file
	cp path/to/sourcefile path/to/targetfile
	
	# move file
	mv path/to/sourcefile path/to/target
	
	# rename file
	mv filename newfilename
	
	# show list of all files and dirs in current location with permissions and human-readable size
	ls -alh
	
	# permissions: read = 4, write = 2, execute = 1
	# example: -rw-r--r--  ==> 644
    
	# modify permissions of a dir and its children
	chmod -R 777 dirname
	
	# modify owner of a file or dir
	chown uid filename
	
	modify group of a file or dir
	chgrp groupname filename
	
	# search files or dirs that contain 'abc' in name
	find -name '*abc*'

	# search files or dirs that contain 'abc' in contents
	find . -name '*' | xargs grep 'abc'
	
	# sort files by last changed time
	find -type f -exec stat --format '%y %n' "{}" \; | sort -nr | head -n 100
	
	# file monitoring
	tail -f filename
	
	# show the disk usage of files and dirs in current location
	du -ah --max-depth=1 ./
	
	# show total disk usage of a dir
	du -sh path/to/dir
	
	# sort files and dirs by disk usage in current location
	du -sh * | sort -h
	
	# upload a dir to a remote machine
	rsync -zrp(tg -X) /path/to/a user@remote.host.com:/path/to/b/
	
	# download dir from another machine
	scp -r user@hosturl:/path/to/dir /path/to/local/dir
	
	# download file from web
	wget file-URL
	
	# compress tar.gz
	tar -zcf abc.tar.gz dirnameOrFilename
	
	# extract tar.gz
	tar -zxf abc.tar.gz
	
	# show list of files in a tar.gz
	tar -tvf abc.tar.gz
	
	# extract one file from a tar.gz
	tar -zxf abc.tar.gz path/to/filename	
	
### System and machine:

	# if I forgot every sudo-users' passwords and cannot enter the machine:
	# restart the maschine, enter "Recovery Mode" and type:
	mount -o remount,rw /
	
	# add path to evironment PATH for this login session
	export PATH=$PATH:/my/path

	# show enironment variables
	env
	
	# show the version of operation system
	uname -a; head -n 1 /etc/issue
	
	# show info about CPU
	cat /proc/cpuinfo
	
	# show mounted devices
	mount | column -t
	
	# list of pci devices
	lspci -tv
	
	# list of usb devices
	lsusb -tv
	
	# show info about memory, cache and swap in MB, update every 10 seconds
	free -m -s 5
	
	# show info about file system
	df -ah
	
	# show info about network interfaces
	ifconfig
	
	# show info about firewalls
	iptables -L
	
	# show info about listened ports (protocol, connections, status, processes)
	netstat -plant
	
	# scan the status of a port (remote)
	nmap -p 8080 example.com
	
	# monitor net transfer
	nethogs
	
	# show info about running processes
	ps -ef 
	
	# monitor running processes
	top
	
### Execution of commands:

	# scheduling tasks:
	# @reboot @yearly ... @daily commands
	# or
	# 30 2 * * * commands (this means, execute the commands at 2:30 every day)
	crontab -e
	
	# show scheduled tasks
	crontab -l
	
	### some methods to record output
	# record all output to a file
	command > output.txt
	# record all output to a file (append)
	command >> output.txt
	# record stderr to a file
	command 2> output.txt
	### record output of some commands to a file without sh-file
	script output.txt
	command1
	command2
	command3
	exit
	### record commands and their outputs and replay that
	# record commands and outputs
	script -t 2>commands.time output.txt
	command1
	command2
	command3
	exit
	# replay the commands (nothing will be re-executed, only the typing and results)
	scriptreplay commands.time output.txt

