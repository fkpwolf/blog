---
layout: post
title: "Linux Command Tips"
date: 2022-6-1
categories:
  - DIY
typora-root-url: ../../../blog
---
### System Info
Neofetch is a command-line system information tool written in bash 3.2+. Neofetch displays information about your operating system, software and hardware in an aesthetic and visually pleasing way. 

Neofetch has lots of dependency. An alternative is screenfetch.

`lsb_release -a` show linux release version and codename

Find Out Ram Speed, Make, Form Factor, Type and Other Information : `dmidecode --type memory`.

### Configurate System
set timezone `sudo dpkg-reconfigure tzdata`

To change locale, use `sudo update-locale LANG=en_US.utf8`. Then ssh can show Chinese characters.
In Ubuntu Server, `locale -a` says zh_CN.utf8. It is unusual. In Putty, "Connection → Data → Environment variables", add LANG/zh_CN.utf8 & LC_CTYPE/zh_CN.utf8. Than Putty can type and show Chinese. 
Mac always ok on Chinese.

### Package management
`dpkg --get-selections | grep java` find which package provides java command

### TTY Device
screen /dev/ttyUSB0 115200
screen -L /dev/tty.SLAB_USBtoUART 115200 — will generate a log file screenlog.0 in current directory.
To activate screen logging function, just press “Ctrl-A” and “H“. (Please be careful, we use capital ‘H’ letter. Using non capital ‘h’, will only create a screenshot of screen in another file named hardcopy).
Ctrl-A, d — will detach from current, but screen still run background. Mac will not release TTL device. If want to attach again, use “screen -r”.
Ctrl-A, : — will enter command mode, run “quit” will exit completely.

`picocom -b 115200 -f h /dev/ttyS0`
To exit, C-a, C-x
compared with screen, picocom can view output history.

no permission to access tty device:
```
sudo usermod -a -G dialout $USER
sudo reboot
```

### SSH
http://stackoverflow.com/questions/12202587/automatically-enter-ssh-password-with-script
sshpass -p "YOUR_PASSWORD" ssh -o StrictHostKeyChecking=no YOUR_USERNAME@SOME_SITE.COM
http://stackoverflow.com/questions/305035/how-to-use-ssh-to-run-shell-script-on-a-remote-machine
ssh user@host <<'ENDSSH'
#commands to run on remote host
ENDSSH

Example of .ssh/config 
```
Host 192.168.51.3* f*
    User fedora 
    IdentityFile ~/.ssh/hp1_id_rsa 
Host f1 
    HostName 192.168.51.31
Host * 
    User fan
```
More details about the config, check https://deepzz.com/post/how-to-setup-ssh-config.html

"permissions are too open” error : `chmod 400 ~/.ssh/id_rsa`

Sometimes ssh it very slow:
    Next authentication method: gssapi-with-mic
    Next authentication method: publickey
第一种认证方法浪费时间，可以在.ssh/config里面加上  PreferredAuthentications publickey。 或者GSSAPIAuthentication no

Connection closed by remote host due to inactivity
```
TCPKeepAlive yes 
ServerAliveInterval 10
```
Set it in client $HOME/.ssh/config file. Now all ssh connections will send a TCPKeepAlive every 10 seconds.

Unable to negotiate with host: no matching key exchange method found. Their offer: diffie-hellman-group1-sha1
--> `ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 user@host`

### File
show details when copy file: `cp --verbose -rf /src/ /dsc/`

`rsync -avz --progress /path/of/source /path/of/target`

A look at rsync performance https://lwn.net/Articles/400489/

https://stackoverflow.com/questions/13713101/rsync-exclude-according-to-gitignore-hgignore-svnignore-like-filter-c
`rsync -a -v --ignore-existing a/ b/ --filter=':- .gitignore' --exclude='/.git'`

mount loop image file
https://www.linuxquestions.org/questions/linux-general-1/how-to-mount-img-file-882386/ only this work for me
https://unix.stackexchange.com/questions/316401/how-to-mount-a-disk-image-from-the-command-line/316407#316407

exclude all “permission denied” messages from “find”
`find /  -name javac 2>/dev/null`

chmod 755, 644, 700  http://learn-web-hosting-domain-name.mygreatname.com/chmod-tutorial.html 

who use this file : `fuser file -v`

### Disk
https://askubuntu.com/questions/587247/how-to-create-a-ext4-partition-for-all-users

Check SATA speed smartctl -a /dev/sda 
nvme-cli package
sudo nvme list
sudo nvme smart-log /dev/nvme0n1

Samba sometimes need create user: `smbpasswd -a linuxsir`. 'root' user is not permitted. Sometimes need `chmod 777 /mnt/sda1` if only read & can’t write.
New samba looks having better UX: just add/append below to /etc/samba/smb.conf. Then ok. Restart is not needed.

### Network
Ubuntu: netplan

### Binary file
To list a binary dependency library: `ldd file`, or use `readelf -a file`.

run `readelf -a portal` and find line as below:
      [Requesting program interpreter: /lib/ld-linux-armhf.so.3]
Then run `ln -s /lib/ld-linux.so.3 /lib/ld-linux-armhf.so.3`.

`readelf -a /usr/arm-linux-gnueabihf/lib/libm.so.6 | grep FP`

`nm a.so`

`strings a.so` #print printable string.

### Bash
```
$ heketi-cli -s http://192.168.51.2:8080 volume list
Id:382bba388e29fbc9fdb3b53b96a203e0    Cluster:1f4e1ef2c5b292aec8cfe282222fa876    Name:vol_382bba388e29fbc9fdb3b53b96a203e0
Id:467863a2abe78ceb8b22dabf7d92c12f    Cluster:1f4e1ef2c5b292aec8cfe282222fa876    Name:vol_467863a2abe78ceb8b22dabf7d92c12f
heketi-cli -s http://192.168.51.2:8080 volume list|grep -oP "Id:\K\w*"| xargs -L 1 heketi-cli -s http://192.168.51.2:8080 volume delete
```
In line, “\K” means deleting the matching “id:” sting. “-L 1” means deleting every volume id. Otherwise, all lines will be executed only once.
https://rook.io/docs/rook/v1.6/ceph-teardown.html another way to run command within foreach.

![Image](/images/2022/bash.png)

### Others
Vim
modify _vimrc in home directory:
```
set guifont=Cascadia_Code:h9
set guioptions -=T
set mouse=
```
This configuration disables mouse support in Vim, which should prevent accidental entry into visual mode when pasting text.

CNTRL+ALT & F1 through F6 you have 7 virtual consoles the seventh is reserved for X.

Edit this file /etc/dhcp/dhclient.conf and set timeout to a reasonable value, like
    timeout 15
The default value of 300 seconds is way too high. The suggested replacement value of 15 was tested and works fine.

`echo -n "STRING" | base64`
-n is to avoid a new line character on the end of the line.

linux nohup http://kumu-linux.github.io/blog/2013/08/06/tmux/

mutliply download tool: `axel`. It also can be used in MacOS.

Apply path `patch -p1 <~/patch1.diff`

Most digital cameras and cell phone add EXIF metadata to the images. EXIF metadata includes camera specifications, settings, location (GPS coordinates) and more.
`exiftool -all= */*.jpg -overwrite_original`

STDERR (standard error) in UNIX and UNIX-like systems is redirected using 2> instead of a single chevron (>). `gpc xxx.pas 2> error.txt`

https://www.cyberciti.biz/faq/what-process-has-open-linux-port/

https://linux.cn/article-5608-1.html 

Check service recent log: `journalctl -u docker -f`
