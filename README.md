# One-liner Goodness

A place for me to put my stuff... meaning bash one-liners.

- [bash](#bash)
- [docker](#docker)
- [ffmpeg](#ffmpeg)
- [git](#git)
- [gpg](#gpg)
- [mysql](#mysql)
- [nmap](#nmap)
- [openssl](#openssl)
- [slowhttptest](#slowhttptest)
- [ssh](#ssh)
- [watch](#watch)
- [wireshark](#wireshark)

##### bash
Find all setuid programs on a computer.
```
sudo find / -xdev \( -perm -4000 \) -type f -print0 | xargs -0 ls -l
```
Batch file rename
```bash
for i in *.mp4; do mv "$i" "${i/s01/s00}"; done
```
List all available commands
```
compgen -c
```
Error out of a bash script if not being run by root
```
if [[ $EUID -ne 0 ]]; then echo "You must be root."; exit 1; fi
```

##### docker
Pull and start a bare-bones CentOS container.
```
docker pull centos
docker run -it centos
```
Commit changes
```
docker commit -m "What did you do to the image" -a "Author Name" container-id repository/new_image_name
```

##### ffmpeg
Batch convert mkvs to mp4s
```
for i in *.mkv; do ffmpeg -i ${i} -c:v copy -c:a copy ${i/\.mkv/\.mp4}; done
```
Combine video and subtitles into an MKV file.
```
brew install mkvtoolnix
mkvmerge -o movie.mkv movie.avi english.srt
mkvmerge -o output.mkv input.mkv --language 0:eng --track-name 0:English subs.srt
```

##### git
Add git lg for a pretty commit history
```
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

##### gpg
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

##### mysql
Print a MySQL performance report via tcpdump & Percona Toolkit
```
tcpdump -s 65535 -x -nn -q -tttt -i eth0 -c 30000 port 3306 | pt-query-digest --report-format profile --type tcpdump
```

##### nmap
Scan for open SSH ports.
```bash
nmap -p22 --open -PN -sV -oG ssh_hosts 10.0.0.0/24
```

##### openssl
Check the expiration date of an SSL certificate.
```bash
echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -dates
```

##### slowhttptest

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

##### ssh
Ghetto VPN
```bash
ssh -D 7070 remotehost
```
Then set your SOCKS proxy as localhost:7070

##### watch
Alert with a beep when an address is unresponsive.
```bash
watch -b ping -c 1 -t 2 google.com
```

##### wireshark
Decode non-standard port as HTTP and show fields
```
tshark port 4567 -d tcp.port==4567,http -T fields -e ip.src -e http.x_forwarded_for -e tcp.dstport -e http.user_agent -e http.request.uri
```
Dump full packet organized by fields in XML
```
tshark -f "dst port 4567" -d tcp.port==4567,http -T pdml
```

