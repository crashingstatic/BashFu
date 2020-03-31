# BashFu
Various bash commands that I have found useful over the years.  
View "RAW" to be able to copy and paste what you need. GitHub Markup and bash syntax don't play nice.  
  
LICENSE: Let's be real, most of this is ripped from StackExchange. You know it. I know it. The original authors would know it. Use the one-liners accordingly.  

#Multiline commenting in a bash script
: <<'END-COMMENT'
sometext or commands to be commented out
END_COMMENT

#Goto wikipedia and download everything related to Easter with a link depth of 3. Convert all of that to a wordlist that can then be used by Hashcat
cewl -c -d 3 -o WordList https://en.wikipedia.org/wiki/Easter

#scan hosts on a network by pinging everything
nmap -sn 192.168.1.1/24

#gives you a list of all commands available to the shell, their permissions, and it's sorted by directory then alphabetically.
ls -la $(echo $PATH | tr ':' ' ') && which | grep -v '/' | grep . | sort

#Shows all SMB shares hosted by NetBIOS
smbclient -L [target IP] -U [username]

#Dig in to a NetBIOS share from the Linux CLI
rpcclient [target IP] -U [username]

#Find all malware disguised as pictures (false positive for anything with "ELF" in the filename)
find / -regextype egrep -iregex ".*\.(jpg|gif|png|jpeg|pdf)" -type f -exec file -p '{}' \; 2>/dev/null | grep ELF | cut -d":" -f1

#process trace, library trace and stack trace dynamically run programs
ptrace, ltrace and strace

#Useful trick for escaping restricted shells on ssh login
ssh -t usrnm@anyIP "bash --noprofile"

#DNS zone transfer
dig -axfr @<DNS you are querying> <target>
dig @<DNS server to query> <target domain> -t AXFR

#Safe shutdown in case of a system hang. It's BUSIER spelled backwards.
Alt+SysRq+R+E+I+S+U+B

#equivalent to 'ps -ef | grep'
pgrep
#pkill will kill a process based on a search

#Watch packet hops in real time
mtr $SOME_IP_ADDRESS

#Backwards search through your command history. Much better than using 'history | grep'
Ctrl+r

#print current ufw/iptables firewall rules
sudo iptables -nvL

#Become root (provided you are a sudoer) for the remainder of the seesion.
sudo -s
sudo su -

screen 'whatever command'
#Press ctrl-a then d to detach the screen. Reattach with 'screen -r'

netstat -lna
#List all ports currently in use

iw dev ath0 station dump
#List wireless clients connected to an access point
#When the wireless card is in AP mode, list the users which are connected to the network.
#It uses the new nl80211 based CLI configuration utility for wireless devices.
#It can be used, for example, in a dd-wrt based router through a telnet session to obtain client information.
arp -an
#will do the same for all ACTIVE connections

#continually check to see if a website is back up, plays a song when it is. This is superior to other methods because it allows for testing of specific HTTP error codes.
wget -qS -t 0 --retry-on-http-error=403 https://www.example.com && afplay somesong.mp3

#Run the last command as root
sudo !!
#Useful when you forget to use sudo for a command. "!!" grabs the last run command

#Run somecommand with the same arguments as the previous command.
#Example:	ls some/path
#		cd !$
somecommand !$

#Displays currently mounted filesystems neatly and with details
mount | column -t

#output your microphone to a remote computer's speaker
dd if=/dev/dsp | ssh -c arcfour -C username@host dd of=/dev/dsp
#This will output the sound from your microphone port to the ssh target computer's speaker port. The sound quality is very bad, so you will hear a lot of hissing.
arecord -f dat | ssh -C user@host aplay -f dat

echo "You can simulate on-screen typing just like in the movies" | pv -qL 10

#save output of a command to an image
ifconfig | convert label:@- ip.png

#kills any process that is using the file
fuser -k filename

#dup finder works by checking size first, then md5 hash
find -not -empty -type f -printf "%s\n" | sort -rn | uniq -d | xargs -I{} -n1 find -type f -size {}c -print0 | xargs -0 md5sum | sort | uniq -w32 --all-repeated=separate

#rip audio from a video file
mplayer -ao pcm -vo null -vc dummy -dumpaudio -dumpfile <output-file> <input-file>

#Copy your ssh public key to a server from a machine that doesn't have ssh-copy-id
cat ~/.ssh/id_rsa.pub | ssh user@machine "mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys"

#Prints graphical tree of your current directory
ls -R | grep ":$" | sed -e 's/:$//' -e 's/[^-][^\/]*\//--/g' -e 's/^/   /' -e 's/-/|/'

#mount an iso in Linux
mount /path/to/file.iso /mnt/cdrom -oloop

#Download all images from a site
wget -r -l1 --no-parent -nH -nd -P/tmp -A".gif,.jpg" http://example.com/images

#Port Knocking!
#Knock on ports to open a port to a service (ssh for example) and knock again to close the port. You have to install knockd.
knock <host> 3000 4000 5000 && ssh -p <port> user@host && knock <host> 5000 4000 3000
#See example config file below.
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
#http://ingles.homeunix.net/software/ost/

#Convert Youtube videos to MP3
youtube-dl -i -x --audio-format mp3 YOUTUBE_URL_HERE

#Use lynx to run repeating website actions
#This command will tell lynx to read keystrokes from the specified file - which can be used in a cronjob to
#auto-login on websites that give you points for logging in once a day *cough cough* (which is why I used -accept_all_cookies).
lynx -accept_all_cookies -cmd_script=/your/keystroke-file
#For creating your keystroke file, use:
lynx -cmd_log yourfile 

#Tar's a directory and then encypts it using openssl
tar c folder_to_encrypt | openssl enc -aes-256-cbc -e > secret.tar.enc
#Decrypt it using:
openssl enc -aes-256-cbc -d < secret.tar.enc | tar x

#Useful for prompting in bash scripts
read -sn 1 -p "Press any key to continue..."

#Find all active IP addresses in a network
nmap -sP 192.168.1.0/24; arp-scan --localnet  | grep "192.168.1.[0-9]* *ether"

#Print a row of characters the width of terminal
printf -vl "%${COLUMNS:-`tput cols 2>&-||echo 80`}s\n" && echo ${l// /-};

#Uses webcam to take pic every 5 minutes, sends to email
while true ; do fswebcam -d /dev/video0 -r 1280x1024 -F 15 - | uuencode $(date +\%Y\%m\%d\%H\%M).jpeg | mail -s "Video surveillance" me@gmail.com ; sleep 300 ; done

#Function to find the fastest dns server
curl -s http://public-dns.info/nameserver/br.csv| cut -d, -f1 | xargs -i timeout 1 ping -c1 -w 1 {} | grep time | sed -u "s/.* from \([^:]*\).*time=\([^ ]*\).*/\2\t\1/g" | sort -n | head -n1

#Automatically update all the installed python packages
pip freeze --local | grep -v '^\-e' | cut -d = -f 1 | xargs -n1 pip install -U --user

#Serve current directory tree at http://$HOSTNAME:8000/
python -m SimpleHTTPServer
#NOTE: If an "index.html" file is present in the directory, SimpleServer will serve t$

#type partial command, kill this command, check something you forgot, yank the command, resume typing.
<ctrl+u> [...] <ctrl+y>

#Mount a temporary ram partition
#Makes a partition in ram which is useful if you need a temporary working space as read/write access is fast.
#Be aware that anything saved in this partition will be gone after your computer is turned off.
mount -t tmpfs tmpfs /mnt -o size=1024m
mount -t [TYPE] -o size=[SIZE] [FSTYPE] [MOUNTPOINT]

#Find Duplicate Files (based on size first, then MD5 hash) 
find -not -empty -type f -printf "%s\n" | sort -rn | uniq -d | xargs -I{} -n1 find -type f -size {}c -print0 | xargs -0 md5sum | sort | uniq -w32 --all-repeated=separate

#Remove duplicate entries in a file without sorting.
awk '!x[$0]++' <file>

#replace spaces in filenames with underscores in the current directory
rename 'y/ /_/' *

#intercept stdout/stderr of another process
strace -ff -e trace=write -e write=1,2 -p SOME_PID

#Check to see if variable VAR is empty
[[ ! -z "$VAR" ]] && echo "not MT" || echo  "MT"

#Compress a folder named "Something" with ultra compression in 7zip into an archive "Something.7z"
7z a -t7z -m0=lzma2 -mx=9 -mmt=on -mfb=64 -md=32m -ms=on Something.7z Something/

#Remove a password from a pdf using ghostscript
gs -q -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -sOutputFile=unencrypted.pdf -c .setpdfwrite -f encrypted.pdf

#Have a virtual keyboard type your clipboard contents
sh -c 'sleep 5.0; xdotool type --clearmodifiers -- "$(xclip -o -selection clipboard)"'

#Compare two lists of checksums, print all elements from list2 that are not in list1
cat list1 list2 | awk '{print $1}' | sort -u | while read i
do
	[[ $(grep "$i" list1) ]] || { grep "$i" list2 ; } 
done

#List all extensions in a directory
find . -type f -name '*.*' | sed 's|.*\.||' | sort -u

#Bash arrays follow modular arithmetic with the first entry located at position 0. To print the last item in an array:
echo "${myarray[-1]}"

#Use this Google Dork to find SQL files
ext:sql "create table" "@gmail.com"

#Firewall-cmd
	#Open port 5044/tcp in firewalld
	sudo firewall-cmd --list-all-zones
	sudo firewall-cmd --permanent --add-port=5044/tcp
	sudo firewall-cmd --reload

	#Remove port 5044/tcp in firewalld
	sudo firewall-cmd --permanent --remove-port=5044/tcp
	sudo firewall-cmd --reload

	#Allow a webserver through firewalld using service listings
	#CAVEAT: "public" is not always the default active zone!
	sudo firewall-cmd --permanent --zone=public --add-service=http
	sudo firewall-cmd --reload

#Leave no trace
sudo journalctl --flush; sudo shred -u .bash_history .ssh/authorized_keys /root/.bash_history; sudo find /var/log/ -type f -exec /bin/shred -u '{}' \; ; touch .bash_history .ssh/authorized_keys; sudo touch /root/.bash_history; history -c; sudo shutdown -h now

#Remove that annoying coloring from output (useful for scripting)
sed 's/\x1B\[[0-9;]*[JKmsu]//g'
