# One-liner Goodness

A place for me to put my stuff... meaning bash one-liners.

- [bash](#bash)
- [cisco](#cisco)
- [defaults](#defaults)
- [docker](#docker)
- [elasticsearch](#elasticsearch)
- [exiftool](#exiftool)
- [ffmpeg](#ffmpeg)
- [git](#git)
- [gpg](#gpg)
- [hpacucli](#hpacucli)
- [macos](#macos)
- [mysql](#mysql)
- [nmap](#nmap)
- [openssl](#openssl)
- [slowhttptest](#slowhttptest)
- [srtedit](#srtedit)
- [ssh](#ssh)
- [watch](#watch)
- [wireshark](#wireshark)

### bash
List files in a dir with too many files.
```
ls -1 -f
```
...and delete them.
```
ls -1 -f | xargs rm
```
Find all setuid programs on a computer.
```
sudo find / -xdev \( -perm -4000 \) -type f -print0 | xargs -0 ls -l
```
Batch file rename
```bash
for i in *.mp4; do mv "$i" "${i/s01/s00}"; done
```
Find all files modified in the last hour, recursively, with details.
```
find . -mmin -60 -type f -exec ls -lhS {} +
```
List all available commands
```
compgen -c
```
Error out of a bash script if not being run by root
```
if [[ $EUID -ne 0 ]]; then echo "You must be root."; exit 1; fi
```
In case your shell session went insane (some script or application turned it into a garbled mess).
```
stty sane
```
- - -
### cisco
Show active interfaces
```
show int status | inc connected
```
Show connected Cisco devices
```
sh cdp neighbors
```
Show logged in AnyConnect users on a Cisco ASA
```
sh vpn-sessiondb svc
```
Trace MAC address to a port
```
show mac address-table | inc 0016.3e17.7369
```
Resequence extended ACL
```
ip access-list resequence 69 10 10
```
Show high traffic IPs
```
sh ip flow top-talkers
```

- - -
### defaults
Enable slow-motion minimize and maximize
```
defaults write com.apple.dock slow-motion-allowed -bool YES && killall Dock
```

- - -
### docker
Pull and start a bare-bones CentOS container.
```
docker pull centos
docker run -it centos
```
Run the stock nginx container
```
docker run -p 8080:80 -d nginx
```
Commit changes
```
docker commit -m "What did you do to the image" -a "Author Name" container-id repository/new_image_name
```
Delete all exited containers
```
docker rm $(docker ps -a -q -f status=exited)
```
Import & export a docker image
```
docker save -o <save image to path> <image name>
docker load -i <path to image tar file>
```

- - -
### elasticsearch
List indices via curl
```
curl -XGET "localhost:9200/_cat/indices?v"
```
Delete indexes for day
```
curl -XDELETE "localhost:9200/*-2017.06.02"
```

- - -
### exiftool
Remove all metadata from an image
```
exiftool -all= image.jpg
```

- - -
### ffmpeg
Batch convert mkvs to mp4s
```
for i in *.mkv; do ffmpeg -i ${i} -c:v copy -c:a copy ${i/\.mkv/\.mp4}; done
```
Combine audio & video files
```
ffmpeg -i video.mp4 -i audio.m4a -c:v copy -c:a copy output.mp4
```
Combine video and subtitles into an MKV file.
```
brew install mkvtoolnix
mkvmerge -o movie.mkv movie.avi english.srt
mkvmerge -o output.mkv input.mkv --language 0:eng --track-name 0:English subs.srt
```
Strip out unwanted audio & subtitles
```
mkvmerge --identify-verbose Buffy.the.Vampire.Slayer.1992.mkv
mkvmerge -o Buffy.the.Vampire.Slayer.1992.new.mkv -a 3 -s 7 Buffy.the.Vampire.Slayer.1992.mkv
```

- - -
### git
Add git lg for a pretty commit history
```
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

- - -
### gpg
Encrypt and decrypt a file
```
gpg -c filename
gpg filename.gpg
```
Encrypt and decrypt a folder
```
gpg-zip -c -o file.gpg dirname
gpg-zip -d file.gpg
```

- - -
### hpacucli
Check the status of an HP Smart Array (RAID)
```
sudo hpacucli ctrl all show config
```

- - -
### macos
Enable VNC from the command line
```
sudo /System/Library/CoreServices/RemoteManagement/ARDAgent.app/Contents/Resources/kickstart -activate -configure -access -off -restart -agent -privs -all -allowAccessFor -allUsers
```
Find memory leaks in a program.
```
sudo leaks -atExit -- ./a.out
```

Extract phone #s from an Address Book .abcddb file
```
sqlite3 -csv -header AddressBook-v22.abcddb "SELECT ZABCDRECORD.ZFIRSTNAME AS 'First Name', ZABCDRECORD.ZLASTNAME AS 'Last Name', ZABCDPHONENUMBER.ZFULLNUMBER AS 'Phone' FROM ZABCDRECORD INNER JOIN ZABCDPHONENUMBER ON ZABCDPHONENUMBER.ZOWNER = ZABCDRECORD.Z_PK;" | sort > phone.csv
```

- - -
### mysql
Print a MySQL performance report via tcpdump & Percona Toolkit
```
tcpdump -s 65535 -x -nn -q -tttt -i eth0 -c 30000 port 3306 | pt-query-digest --report-format profile --type tcpdump
```

Take a dump
```
mysqldump --master-data --single-transaction --add-drop-database --routines --all-databases 2>> $BACKUPDIR/error_dump.log | gzip > $BACKUPDIR/fulldump_$(date +%F).sql.gz
# --dump-slave to get master's position
# --master-data to get the slave's position
```

- - -
### nmap
Scan for open SSH ports.
```bash
nmap -p22 --open -PN -sV -oG ssh_hosts 10.0.0.0/24
```

- - -
### openssl
Check the expiration date of an SSL certificate.
```bash
echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -dates
```

- - -
### slowhttptest

###### Slowloris mode
```
slowhttptest -c 1000 -H -i 10 -r 200 -t GET -u http://localhost:8080 -x 24 -p 3
```
Out of the box apache being attacked by this will have their process table filled with "R" (Reading Request) and will quickly become unresponsive.  An nginx reverse proxy blocks this attack.

###### Slow message body
```
slowhttptest -c 1000 -B -i 110 -r 200 -s 8192 -t FAKEVERB -u http://localhost:8080 -x 10 -p 3
```
Out of the box apache being attacked by this will have their process table filled with “W” (Sending Reply) and will quickly become unresponsive.  An nginx reverse proxy blocks this attack.


###### Apache Range Header attack
```
slowhttptest -R -u http://localhost:8080 -t HEAD -c 1000 -a 10 -b 3000 -r 500
```
This attack is effectively invisible to the apache process table and does not appear to directly effect the site being attacked.  However, it causes apache CPU usage on the host to go up considerably.  An nginx reverse proxy blocks this attack.
- - -
### srtedit
Delay an srt file by n milliseconds
```
srtedit TVShow.S03E02.eng.srt -D 600
```
Hasten an srt file by n milliseconds
```
srtedit TVShow.S03E02.eng.srt -H 600
```

- - -
### ssh
Ghetto VPN
```bash
ssh -D 7070 remotehost
```
Then set your SOCKS proxy as localhost:7070

Tunnel a remote port to a local one
```
ssh  -L 6999:localhost:5900 username@remotehost
```

Tunnel port 3128 on localhost to port 8080 on remoteHost.
```
ssh -R 8080:localhost:3128 remoteHost
```

Mount a directory over ssh
```
sudo sshfs -o allow_other,reconnect,ServerAliveInterval=15,ServerAliveCountMax=3 user@host:/DirToMount /localMountPoint
```

- - -
### watch
Alert with a beep when an address is unresponsive.
```bash
watch -b ping -c 1 -t 2 google.com
```

- - -
### wireshark
Decode non-standard port as HTTP and show fields
```
tshark port 4567 -d tcp.port==4567,http -T fields -e ip.src -e http.x_forwarded_for -e tcp.dstport -e http.user_agent -e http.request.uri
```
Dump full packet organized by fields in XML
```
tshark -f "dst port 4567" -d tcp.port==4567,http -T pdml
```

