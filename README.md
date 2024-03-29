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

```bash

	# create an user with home-dir
	useradd -m -s /bin/bash/ username
	
	# create a super user, make him the SAME user as root and have home-dir
	# (BUT BE CAREFUL OF DOING THIS ENLESS YOU KNOW WHAT YOU ARE DOING)
	# to delete this super user: change the uid from 0 to other in the /etc/passwd
	useradd -u 0 -g 0 -o -m -s /bin/bash username 
	
	# change the password of an user	
	passwd username
	
	# disable login for an user; sudo -u -i still works
	passwd -d username
	
	# delete an user and his home-dir
	userdel -r username  
	
	# add a group (with --system is a system group)
	addgroup groupname  
	
	# add an user to a group
	usermod -a -G groupname username
	
	# delete an user from a group
	gpasswd -d username groupname

	# create a only-local-login-user; 
	# if other user should use this special user account through sudo -u, then edit sudo config 
	useradd -m -s /bin/bash username
	usermod -L username
	
	# login to other user with current env
	su username
	# login to other user with his env
	su -l username
	sudo -u username -i
	
	# show log of users' login
	last -a
	# show log of users' most recently login
	lastlog
	
	# show all users' name, id, sh/bash
	cat /etc/passwd
	
	# show all groups' name, id, assigned users
	cat /etc/group

	# login remote server without entering password
	ssh-keygen -t rsa -f keyFileName_rsa
	ssh-copy-id -i keyFileName_rsa.pub username@remote
	ssh username@remote
	
	# remove public keys on remote machine
	vim $HOME/.ssh/authorized_keys
	# then delete no longer used keys
	
	# add ssh private key to terminal session (useful for login to server from another server)
	eval $(ssh-agent -s)
	ssh-add ~/.ssh/other_id_rsa
	
	# copy file content to clipboard
	cat fileName | xclip -sel clip

```
	
### APT and dpkg stuffs:
	
```bash

	# update apt repository data
	apt-get update
	
	# install a package with version number
	apt-get install abc=5.5.29...
	dpkg -i *.deb
	
	# search a package with name
	apt-cache search packagename
	
	# uninstall a package
	apt-get remove packagename
	dpkg -r packagename
	
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
	
	### upgrade system
	# check if new version available
	do-release-upgrade -c
	# upgrade
	do-release-upgrade
```

### Operations for files or directories:

```bash

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
	
	# file/folder operations by name using regex
	find dir/ -maxdepth 1 -iregex "pattern" -exec cp {} distdir/ \;
	# example:
	find . -maxdepth 1 -type d -iregex ".*00[0-1][1-2].*" -exec cp -r {} dist/ \; 
	# copy folders with name contains *0001*, *0002*, *0011*, *0012*
	
	# show list of all files and dirs in current location with permissions and human-readable size
	ls -alh
	
	# permissions: read = 4, write = 2, execute = 1
	# example: -rw-r--r--  ==> 644
    
	# modify permissions of a dir and its children
	chmod -R 777 dirname
	
	# modify owner of a file or dir
	chown uid filename
	
	# modify group of a file or dir
	chgrp groupname filename
	
	# make new files and directories in one directory also having the same group
	chmod g+s dirname
	
	# lock/unlock file or dir (with sudo also can not be deleted) 
	chattr +i filename/dir
	chattr -i filename/dir
	
	# only adding content to file or dir allowed
	chattr +a filename/dir
	chattr -a filename/dir
	
	# search files or dirs that contain 'abc' in name
	find -name '*abc*'
	
	# search files with name patterns
	find ./src/ -type f -regextype emacs -regex ".*[^e][^c]\.\(ts\|html\|scss\)$"
	
	# search files that contain 'abc' in contents
	find . -name '*' | xargs grep -nP -C 3 'abc'
	# or
	fgrep -nr -C 3 somethingToSearch *.*
	
	# sort files by last changed time
	find -type f -exec stat --format '%y %n' "{}" \; | sort -nr | head -n 100
	
	# replace strings in all found files using regular expression
	find -type f -name "*" -exec sed -i 's/someStringsToBeReplaced/somethingNew/g' {} \;
	
	# file monitoring
	tail -f filename
	
	# show users who are using a specified file
	fuser -u -m filename
	
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
	
	# open file explorer 
	nautilus pathToFolder
	
```

### System and machine:

```bash

	# if I forgot every sudo-users' passwords and cannot enter the machine:
	# restart the maschine, enter "Recovery Mode" and type:
	mount -o remount,rw /
	
	# add path to evironment PATH for this login session
	export PATH=$PATH:/my/path

	# show enironment variables
	env
	
	# show the version of operation system
	uname -a; head -n 1 /etc/issue
	uname -a; lsb_release -a
	
	# show full info about motherboard (-t 2: base info; -t 17: memory info; ...)
	sudo dmidecode -t 2/17... | more
	
	# show info about CPU
	cat /proc/cpuinfo
	
	# show mounted devices
	mount | column -t
	
	### share folders to other machine
	## on server:
	apt install nfs-common nfs-kernel-server rpcbind
	# add info in /etc/exports:
	/path/to/shared/folder clientNameOrIP(rw,sync,no_subtree_check)
	/etc/init.d/nfs-kernel-server restart
	# on client:
	apt install nfs-common
	# check if server with folder visible
	showmount -e serverNameOrIP
	# mount server's folder
	mount -o soft,intr,rsize=8192,wsize=8192 serverNameOrIP:/path/to/shared/folder /path/to/shared/folderClient
	
	# use lshw
	lshw -c display/memory/cpu/...
	
	# use nvidia specific commands to show info of nvidia graphic cards
	nvidia-smi
	
	# list of pci devices
	lspci -tv
	
	# list of usb devices
	lsusb -tv
	
	# show info about memory, cache and swap in MB, update every 10 seconds
	free -m -s 5
	
	# add large swap file for memory
	sudo fallocate -l 32G /swapfile
	sudo chmod 600 /swapfile
	sudo mkswap /swapfile
	sudo swapon /swapfile
	sudo echo "\n/swapfile swap swap defaults 0 0" >> /etc/fstab
	
	# show info about file system
	df -ahT
	
	# show info about disks
	hdparm -i/-I /dev/sda1
	
	# format disk (example: USB)
	# unmount first
	umount /dev/sdd1
	# format to ntfs
	mkfs.ntfs /dev/sdd1
	# format to ext4
	mkfs.ext4 /dev/sdd1
	
	# show info about network interfaces
	ifconfig
	
	# show info about firewalls
	iptables -L
	
	# show info about listened ports (protocol, connections, status, processes)
	netstat -plant
	
	# scan the status of a port (remote)
	nmap -p 8080 example.com
	
	# monitor net transfer for processes
	nethogs
	
	# monitor net transfer for all network-interfaces
	bmon
	
	# show info about running processes
	ps -ef
	ps -eaux
	
	# sort processes by current cpu usage using ps
	ps -eaux | sort -k 3
	
	# sort processes by current mem usage using ps
	ps -eaux | sort -k 4
	
	# monitor running processes
	top
	
	# limit top by specific process-name
	top -p `pgrep processName | tr "\\n" "," | sed "s/,$//"`
	
	# calculate ram usage of processes that contain the same processName
	top -n1 -b | grep processName | sort -u -rk 9 | awk '{print $10}' | sed "s/,/\./" | paste -sd+ - | bc
	
	# check errors of system or services
	systemctl status serviceName
	journalctl -xe
	
	# increse max number of watched files, inotify watchers reaches limit:
	# modify or add fs.inotify.max_user_watches in /etc/sysctl.conf
	echo fs.inotify.max_user_watches=524288 | tee -a /etc/sysctl.conf
	sysctl -p
	
	# increase number of opened files for all/specific users
	# add in /etc/security/limits.conf
	*               soft    nofile            2048
	*               hard    nofile            2048
	
```

### Hardware:
	
```bash

	# Usb sound device not working
	## install PluseAudio Volume Control -> Configuration-Tab: set profiles of other sound devices "off"
	
	# fix NTFS Drive read-only error after used by Windows
	# get path of the device, unmount, then:
	ntfsfix /dev/....
	
	# setup wlan over lan
	## change interface metrics of wlan greater than lan
	
```

### Execution of commands:

```bash

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
	# record output to file and show it in console at the same time
	command | tee output.txt
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
```

## Often used programs' commands:
### Git:

```bash
	# init a new repository
	git init
	# init a new repository on server-site
	git init --bare
	
	# add github, bitbucket, ... to existing local git-repo
	git remote add origin ssh://git@...
	git push -u origin master
	
	# push tag to remote
	git push origin tagName
	# push a moved tag
	git push origin tagName --force
	
	# change remote URL
	git remote set-url origin ssh://git@....
	
	# push all branches to all remote repositories
	git remote | xargs -L1 git push
	# push one branch (e.g. master) to all remote repositories
	git remote | xargs -L1 -I R git push R master
	
	## commit changes
	# stage a file
	git add filename
	# stage all changed files
	git add -A
	# commit with message
	git commit -m "commit comment"
	## or
	git commit -a -m "commit comment"
	# make a empty commit (useful for triggering a hook)
	git commit --allow-empty -m "comment"
	
	# unstage all staged files
	git reset
	
	# change message of last commit
	git commit --amend
	
	# git reset commit HARD (reset pointer and files) and SOFT (reset only pointer)
	git reset HEAD^ --hard (--soft)
	
	# restore commit after reset
	git reflog
	git reset 'HEAD@{number}' # HEAD@{number} is the reference of throwed commit in reflog
	
	# change message of a specific commit
	git rebase -i HEAD~number #number: how far is the to changed commit referenced from HEAD
	# change 'pick' with 'reword', then save.
	# change message, then save.
	
	# change commt message to remote: first change message like above locally, then
	git push --force originOrOther branchOfTheCommit
	
	# recursive add empty directories to git-repository, except the directories in .git/
	find . -type d -empty -not -path "./.git/*" -exec touch {}/.gitkeep \;
	
	# show branches
	git show-branch
	
	# create branch and switch to it
	git checkout -b branchName
	
	# delete local branch
	git branch -d branchName
	# delete remote branch
	git push originOrOther --delete branchName
	# set branch to other commit/branch...
	git branch -f branchName otherCommitOrBranch
	
	# merge other branch with current branch
	git merge otherBranchName
	# merge other branch using changes of current branch
	git merge -s ours
	# merge other branch using strategy "recursive", but only by conflicts using changes of current branch
	git merge -X ours
	# merge other branch using strategy "recursive", but only by conflicts using changes of incoming branch
	git merge -X theirs
	
	# fast forward merge into other branch without checkout it first
	git fetch . master:release # . means using local repo; merge master into release
	
	# make commits as if they took place after other commits in other branch were made
	git rebase origin/master master
	
	# use stash to store changes temporarily
	git stash
	git stash list
	git stash apply [stash@{num}]
	# delete all stashed changes
	git stash clear
	# show changes in a stash
	git stash show -p
	git stash show -p stash@{num}
	
	# list commits which change a spec file
	git log --follow fileName
	
	# list commits with stat of changes
	git log --stat
	
	# check changes of lines in commits
	git log -L110,120:src/.../.../filename
	
	# show diff between 2 commits, branches or changed files
	git diff commitHash1 commitHash2
	git diff HEAD commitHash
	git diff commit1 commit2 -- path/to/fileName
	
	# show merge request locally
	git diff master...branch
	
	# show changes of master since branch was created
	git diff branch...master
	
	# remove "^m" by git diff
	git config --global core.whitespace cr-at-eol
	
	# try to cache password for the next n seconds (useful for https git remote)
	git config credential.helper 'cache --timeout=2592000'  (30 days)
	
	# advanced log with graph in terminal 
	# (THANK FOR https://stackoverflow.com/questions/1838873/visualizing-branch-topology-in-git/34467298#34467298)
	git log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(reset) %C(bold green)(%ar)%C(reset) %C(bold cyan)(committed: %cD)%C(reset) %C(auto)%d%C(reset)%n''          %C(white)%s%C(reset)%n''          %C(dim white)- %an <%ae> %C(reset) %C(dim white)(committer: %cn <%ce>)%C(reset)'
	
	# search code in one commit and particular file type
	git grep <regexp> <commit> -- '*.ts'
	# search code in all commit history
	git grep <regexp> $(git rev-list --all)
	# and more THANKS TO https://stackoverflow.com/a/2929502
	
	# show which author in which commit last modified lines of a file
	git blame -L300,+1 fileName
	
	# list all authors in one repo (n: sort by num of commits)
	git shortlog -sn | awk -F ' ' '{print $2, $3}'
	
	# count changing lines of authors
	git log --format='%aN' | sort -u | while read author; do echo "$author " && git log --author="$author" --pretty=tformat: --numstat -- . ':!to-be-excluded-dir' | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "lines added: %s, removed: %s, total: %s\n", add, subs, loc }'; done
	
	# count lines of authors based on HEAD
	git ls-files | while read f; do git blame -w -M -C -C --line-porcelain "$f" | grep "^author "; done | sort -f | uniq -ic | sort -n
	
	# list num of commits and the date of last commit of each author
	git log --format='%aN <%aE>' | sort -u | while read name; do; echo -ne "$name\t\t\t"; echo -en `git log --author="$name" --pretty=tformat: --numstat | wc -l`; echo -e "\t" `git log --author="$name" --max-count=1 --format=%cd`; done
	
	# create a patch
	git format-patch <commit/branch> --stdout > named.patch
	# check if a patch can be applied
	git apply --check named.patch
	# check patch info
	git apply --stat named.patch
	# apply patch
	git apply named.patch
	
	# remove file from index
	git rm --cached filename
	
```

### Apache2:

```bash

	# start, stop
	service apache2 start/stop
	
	# restart gracefully
	service apache2 reload
	
	# enable, disable modules
	a2enmod modName
	a2dismod modName
	
	# enable, disable sites
	a2ensite siteName
	a2dissite siteName
	
	# enable, disable config
	a2enconf confName
	a2disconf confName
	
	# show enabled modules or sites(settings in config files)
	apache2tcl -M / -S
```

### Docker
	
```bash

	# get docker system info
	docker system info
	
	# show space usages
	docker system df
	docker system df -v
	
	# create image using Dockerfile
	docker build -t tagName pathContainsDockerfile
	
	# create image using running/stopped container
	docker commit -a "author info" -m "message info" containerName imageName[:tag]
	
	# transfer image not using registry
	docker save --output imageName.tar imageName
	docker load --input imageName.tar
	
	# run an image with portforwarding and run it in background
	# there are a lot of other useful parameters available for this command such as -v --restart etc.
	docker -d -p 8888:80 imageTagName
	
	# list all containers
	docker ps -a
	
	# list all images
	docker images -a
	
	# stop a container
	docker stop containerID
	# stop a container forcely
	docker kill containerID
	
	# delete container
	docker rm containerID
	
	# delete container with volume
	docker rm -v containerID
	
	# delete all containers
	docker rm $(docker ps -a -q)
	
	# delete an image 
	docker rmi imageTagName
	
	# delete all images
	docker rmi $(docker images -a -q)
	
	# manage volumes in /var/lib/docker/volume 
	docker volume ls
	docker volume rm volumeName
	docker volume inspect volumeName
	
	# find out all mounts and volumes of a container
	docker inspect -f {{ .Mounts }} containderId
	
	### clean up (thanks to https://gist.github.com/bastman/5b57ddb3c11942094f8d0a97d461b430)
	# clean temp containers: mostly created by running Dockerfile
	docker rm -v $(docker ps -qa --filter "status=exited" --filter "name=_")
	# clean temp images
	docker rmi $(docker images | grep "none" | awk '/ / { print $3 }')
	# clean unsed volumns
	docker volume rm $(docker volume ls -q --filter "dangling=true")

	# execute commands in container
	docker exec containerName bash -c "commands"
	# execute commands in container with console
	docker exec -it containerName bash -c "commands"
	
```

## some small tricks

### workaround for meld encoding problems:

```bash

	gsettings set org.gnome.meld detect-encodings "['UTF-8','ISO-8859-1','UTF-16']"
```

### Port forwarding over SSH: 
	
```bash

	# connect 3306 on example.org with username through local port 3316 
	ssh -f username@example.org -L 3316:localhost:3306 -N
```

### adding bash script to Unity panel as quick laucher
	
	# THANK TO https://askubuntu.com/questions/141229/how-to-add-a-shell-script-to-launcher-as-shortcut

### menu bar in gnome top bar
	# use the extention Gnome Global Application Menu(HUD for Gnome) by extensions.gnome.org 

### install typescript support for vim

```bash
	git clone https://github.com/pangloss/vim-javascript.git ~/.vim/pack/vim-javascript/start/vim-javascript
	git clone https://github.com/leafgarland/typescript-vim.git ~/.vim/pack/typescript/start/typescript-vim
```

### Windows-Desktop running in VirtualBox under Ubuntu:

```bash

	# auto setup the resolution to the size of the VM-window in the host(Ubuntu)
	# get the name of the running vm
	VBoxManage list runningvms 
	VBoxManage controlvm "name_of_the_running_vm" setvideomodehint 1280 900 32 

```

### WSL 

Change vhdx file location using WSL export/import:

```
	wsl --export Ubuntu e:\backup\ubuntu.tar
	wsl --unregister Ubuntu
	mkdir e:\wsl
	wsl --import Ubuntu e:\wsl\ e:\backup\ubuntu.tar
	cd %userprofile%\AppData\Local\Microsoft\WindowsApps
	ubuntu config --default-user <username>
```

### Setup the number of workspaces in Ubuntu : 3*3
	
```bash

	dconf write /org/compiz/profiles/unity/plugins/core/hsize 3
	dconf write /org/compiz/profiles/unity/plugins/core/vsize 3
	restart
	
	# actually I don't use the above mentioned 3 commands any more because I found 
	# the unity tweak tool.	
	apt-add-repository ppa:tualatrix/ppa
	apt-get update
	apt-get install ubuntu-tweak

```

### setup mysql open_files_limit: 

##### if using systemd: (Thanks minni's answer https://stackoverflow.com/a/35515570)

In the file /lib/systemd/system/mysql.service you have to add this 2 lines in the [Service] section at the end:

	LimitNOFILE = infinity
	LimitMEMLOCK = infinity

After this restart systemctl and mysql:

```bash

	systemctl daemon-reload
	/etc/init.d/mysql restart
	
```

To check if the configuration is effective, you can get the parameter from the running mysql process like this:

```bash

	cat /proc/$(pgrep mysqld$)/limits | grep files

```

##### or: (Thanks for https://duntuk.com/how-raise-ulimit-open-files-and-mysql-openfileslimit)

### change mysql default `datadir`:

stop mysql:

```bash

	service mysql stop

```

copy mysql data dir:

```bash

	cp -R -p /var/lib/mysql /new/path
	
```

edit conf file, change /var/lib/mysql to /new/path:

```bash

	vim /etc/mysql/mysql.conf.d/mysqld.cnf
	vim /etc/apparmor.d/usr.sbin.mysqld
	# reload apparmor conf
	/etc/init.d/apparmor reload

```

restart mysql:

```bash
	service mysql start
```

### certificates:

#### use certbot:

```bash

	# install certbot for apache
	apt install python-certbot-apache
	# install a certificate for a domain
	certbot --apache
```

	
#### create certificate manually:

create root CA:
	
```bash
	openssl genrsa -aes256 -out rootCA-privateKey.pem 2048
	openssl req -new -key rootCA-privateKey.pem -out rootCA-req.csr -subj "/C=Country/ST=City/L=Land/O=someCompany/OU=CompanyDepartment/CN=domainName"
	openssl x509 -req -days 3650 -sha1 -extensions v3_ca -signkey rootCA-privateKey.pem -in rootCA-req.csr -out rootCA-ca.cer

```

use root CA to encrypt server certificate:

```bash
	openssl genrsa -aes256 -out server-privateKey.pem 2048
	openssl req -new -key server-privateKey.pem -out server-req.csr -subj "/C=Country/ST=City/L=Land/O=someCompany/OU=CompanyDepartment/CN=domainName"
	openssl x509 -req -days 3650 -sha1 -extensions v3_ca -CA rootCA-ca.cer -CAkey rootCA-privateKey.pem -CAserial rootCA-ca.srl -CAcreateserial -in server-req.csr -out server-cer.cer

```

use root CA to encrypt client certificate:

```bash
	openssl genrsa -aes256 -out client-privateKey.pem 2048
	openssl req -new -key client-privateKey.pem -out client-req.csr -subj "/C=Country/ST=City/L=Land/O=someCompany/OU=CompanyDepartment/CN=domainName"
	openssl x509 -req -days 3650 -sha1 -extensions v3_ca -CA rootCA-ca.cer -CAkey rootCA-privateKey.pem -CAserial rootCA-ca.srl -CAcreateserial -in client-req.csr -out client-cer.cer

```

export p12:

```bash
	openssl pkcs12 -export -clcerts -inkey client-privateKey.pem -in client-cer.cer -out client-cer.p12
```

#### print certificates:

```bash
	keytool -printcert -file cert.pem
```

#### asym. encrypting:

```bash
	# generate private key
	openssl genpkey -algorithm RSA -out private-key.pem -pkeyopt rsa_keygen_bits:2048
	# generate public key of the private key
	openssl rsa -pubout -in private-key.pem -out public-key.pem
	# encrypt with public key
	openssl rsautl -encrypt -inkey public-key.pem -pubin -in some.txt -out some.txt.encrypted
	# decrypt with private key
	openssl rsautl -decrypt -inkey private-key.pem -in some.txt.encrypted -out some.txt
	# encrypt with private key
	cat some.txt |  openssl rsautl -inkey private-key.pem -sign > some.txt.enc-with-private-key.encrypted
	# decrypt with public key
	openssl rsautl -inkey public-key.pem -pubin -in some.txt.enc-with-private-key.encrypted
```


### awk
	
```bash
	# analyse apache error log file: count accesses by ip and sort the results
	awk -F "[][{}]" '{print $8}' error.log | awk -F ":" '{print $1}' | sort | uniq -c | sort -n 	
```

### usb tethering
	
```bash
	# 1. turn on usb tethering on phone
	# 2. find out network adaptor name
	ifconfig -a
	# or
	ip link
	# then
	ifconfig enp0xxxxxxxxx up
	# 3. add config in /etc/network/interfaces
	# auto enp0xxxxxxxxx
	# iface enp0xxxxxxxxx inet dhcp
	# 4. activate
	ifup enp0xxxxxxxxx

```

### AC 1200 rtl88x Wifi driver

	# check https://github.com/cilynx for newest driver and follow the readme
######################
