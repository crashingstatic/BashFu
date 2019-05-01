# BashFu
Various bash commands that I have found useful over the years.  
LICENSE: Let's be real, most of this is ripped from StackExchange. You know it. I know it. The original authors would know it. Use the one-liners accordingly.  
  
#### Multiline commenting in a bash script
: &lt;&lt;'END-COMMENT'  
sometext or commands to be commented out  
END_COMMENT  
  
cewl -c -d 3 -o WordList https://en.wikipedia.org/wiki/Easter
#### Goto wikipedia and download everything related to Easter with a link depth of 3. Convert all of that to a wordlist that can then be used by Hashcat

nmap -sn 192.168.1.1/24
#### scan hosts on a network by pinging everything

ls -la $(echo $PATH | tr ':' ' ') && which | grep -v '/' | grep . | sort
#### gives you a list of all commands available to the shell, their permissions, and it's sorted by directory then alphabetically.

smbclient -L [target IP] -U [username]
#### Shows all SMB shares hosted by NetBIOS

rpcclient [target IP] -U [username]
#### Dig in to a NetBIOS share from the Linux CLI

find / -regextype egrep -iregex ".*\.(jpg|gif|png|jpeg|pdf)" -type f -exec file -p '{}' \; 2>/dev/null | grep ELF | cut -d":" -f1
#### Find all malware disguised as pictures (false positive for anything with "ELF" in the filename)

ptrace, ltrace and strace
#### process trace, library trace and stack trace dynamically run programs

ssh -t usrnm@anyIP "bash --noprofile"
#### Useful trick for escaping restricted shells on ssh login

#### DNS zone transfer
dig -axfr @&lt;DNS you are querying> &lt;target>
dig @&lt;DNS server to query> &lt;target domain> -t AXFR

Alt+SysRq+R+E+I+S+U+B
#### Safe shutdown in case of a system hang. It's BUSIER spelled backwards.

pgrep
#### equivalent to 'ps -ef | grep'
#### pkill will kill a process based on a search

mtr $SOME_IP_ADDRESS
#### Watch packet hops in real time

Ctrl+r
#### Backwards search through your command history. Much better than using 'history | grep'

sudo iptables -L -v -n
#### print current ufw/iptables firewall rules

sudo -s
sudo su -
#### Become root (provided you are a sudoer) for the remainder of the seesion. Useful for priviledge escalation.

screen 'whatever command'
#### Press ctrl-a then d to detach the screen. Reattach with 'screen -r'

netstat -lntu
#### List all ports currently in use

iw dev ath0 station dump
#### List wireless clients connected to an access point
#### When the wireless card is in AP mode, list the users which are connected to the network.
#### It uses the new nl80211 based CLI configuration utility for wireless devices.
#### It can be used, for example, in a dd-wrt based router through a telnet session to obtain client information.
arp -an
#### will do the same for all ACTIVE connections

wget -qS -t 0 --retry-on-http-error=403 https://www.uma.edu && afplay somesong.mp3
#### continually check to see if a website is back up, plays a song when it is. This is superior to other methods because it allows for testing of specific HTTP error codes.

sudo !!
#### Run the last command as root
#### Useful when you forget to use sudo for a command. "!!" grabs the last run command

somecommand !$
#### Run somecommand with the same arguments as the previous command.
#### Example:	ls some/path
#### 		cd !$

mount | column -t
#### Displays currently mounted filesystems neatly and with details

dd if=/dev/dsp | ssh -c arcfour -C username@host dd of=/dev/dsp
#### output your microphone to a remote computer's speaker
#### This will output the sound from your microphone port to the ssh target computer's speaker port. The sound quality is very bad, so you will hear a lot of hissing.
arecord -f dat | ssh -C user@host aplay -f dat

echo "You can simulate on-screen typing just like in the movies" | pv -qL 10

ifconfig | convert label:@- ip.png
#### save output of a command to an image

fuser -k filename
#### kills any process that is using the file

find -not -empty -type f -printf "%s\n" | sort -rn | uniq -d | xargs -I{} -n1 find -type f -size {}c -print0 | xargs -0 md5sum | sort | uniq -w32 --all-repeated=separate
#### dup finder works by checking size first, then md5 hash

mplayer -ao pcm -vo null -vc dummy -dumpaudio -dumpfile &lt;output-file> &lt;input-file>
#### rip audio from a video file

cat ~/.ssh/id_rsa.pub | ssh user@machine "mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys"
#### Copy your ssh public key to a server from a machine that doesn't have ssh-copy-id

ls -R | grep ":$" | sed -e 's/:$//' -e 's/[^-][^\/]*\//--/g' -e 's/^/   /' -e 's/-/|/'
#### Prints graphical tree of your current directory

mount /path/to/file.iso /mnt/cdrom -oloop
#### mount an iso in Linux

wget -r -l1 --no-parent -nH -nd -P/tmp -A".gif,.jpg" http://example.com/images
#### Download all images from a site

knock &lt;host> 3000 4000 5000 && ssh -p &lt;port> user@host && knock &lt;host> 5000 4000 3000
#### Port Knocking!
#### Knock on ports to open a port to a service (ssh for example) and knock again to close the port. You have to install knockd.
#### See example config file below.
[options]
logfile = /var/log/knockd.log
[openSSH]
sequence = 3000,4000,5000
seq_timeout = 5
command = /sbin/iptables -A INPUT -i eth0 -s %IP% -p tcp --dport 22 -j ACCEPT
tcpflags = syn
[closeSSH]
sequence = 5000,4000,3000
seq_timeout = 5
command = /sbin/iptables -D INPUT -i eth0 -s %IP% -p tcp --dport 22 -j ACCEPT
tcpflags = syn
#### http://ingles.homeunix.net/software/ost/

youtube-dl -t --extract-audio --audio-format mp3 YOUTUBE_URL_HERE
#### Convert Youtube videos to MP3

lynx -accept_all_cookies -cmd_script=/your/keystroke-file
#### Use lynx to run repeating website actions
#### This command will tell lynx to read keystrokes from the specified file - which can be used in a cronjob to
#### auto-login on websites that give you points for logging in once a day *cough cough* (which is why I used -accept_all_cookies).
#### For creating your keystroke file, use:
lynx -cmd_log yourfile 

tar c folder_to_encrypt | openssl enc -aes-256-cbc -e > secret.tar.enc
#### Tar's a directory and then encypts it using openssl
#### Decrypt it using:
openssl enc -aes-256-cbc -d &lt; secret.tar.enc | tar x

read -sn 1 -p "Press any key to continue..."
#### Useful for prompting in bash scripts

nmap -sP 192.168.1.0/24; arp-scan --localnet  | grep "192.168.1.[0-9]* *ether"
#### Find all active IP addresses in a network

printf -vl "%${COLUMNS:-`tput cols 2>&-||echo 80`}s\n" && echo ${l// /-};
#### Print a row of characters the width of terminal

while true ; do fswebcam -d /dev/video0 -r 1280x1024 -F 15 - | uuencode $(date +\%Y\%m\%d\%H\%M).jpeg | mail -s "Video surveillance" me@gmail.com ; sleep 300 ; done
#### Uses webcam to take pic every 5 minutes, sends to email

#### Function to find the fastest dns server
curl -s http://public-dns.info/nameserver/br.csv| cut -d, -f1 | xargs -i timeout 1 ping -c1 -w 1 {} | grep time | sed -u "s/.* from \([^:]*\).*time=\([^ ]*\).*/\2\t\1/g" | sort -n | head -n1

Automatically update all the installed python packages
#### pip freeze --local | grep -v '^\-e' | cut -d = -f 1 | xargs -n1 pip install -U --user

#### Serve current directory tree at http://$HOSTNAME:8000/
python -m SimpleHTTPServer
#### NOTE: If an "index.html" file is present in the directory, SimpleServer will serve t$

#### type partial command, kill this command, check something you forgot, yank the command, resume typing.
&lt;ctrl+u> [...] &lt;ctrl+y>

#### Mount a temporary ram partition
#### Makes a partition in ram which is useful if you need a temporary working space as read/write access is fast.
#### Be aware that anything saved in this partition will be gone after your computer is turned off.
mount -t tmpfs tmpfs /mnt -o size=1024m
mount -t [TYPE] -o size=[SIZE] [FSTYPE] [MOUNTPOINT]

#### Find Duplicate Files (based on size first, then MD5 hash) 
find -not -empty -type f -printf "%s\n" | sort -rn | uniq -d | xargs -I{} -n1 find -type f -size {}c -print0 | xargs -0 md5sum | sort | uniq -w32 --all-repeated=separate

#### Remove duplicate entries in a file without sorting.
awk '!x[$0]++' &lt;file>

#### replace spaces in filenames with underscores in the current directory
rename 'y/ /_/' *

#### intercept stdout/stderr of another process
strace -ff -e trace=write -e write=1,2 -p SOME_PID
