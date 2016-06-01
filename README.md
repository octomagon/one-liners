# One-liner Goodness

A place for me to put my stuff... meaning bash one-liners.

- [bash](#bash)
- [ffmpeg](#ffmpeg)
- [gpg](#gpg)
- [nmap](#nmap)
- [openssl](#openssl)
- [ssh](#ssh)
- [watch](#watch)
- [wireshark](#wireshark)

### bash
Find all setuid programs on a computer.
```
sudo find / -xdev \( -perm -4000 \) -type f -print0 | xargs -0 ls -l
```
Batch file rename
```bash
for i in *.mp4; do mv "$i" "${i/s01/s00}"; done
```

### ffmpeg
Batch convert mkvs to mp4s
```
for i in *.mkv; do ffmpeg -i ${i} -c:v copy -c:a copy ${i/\.mkv/\.mp4}; done
```
Combine video and subtitles into an MKV file.
```
brew install mkvtoolnix
mkvmerge -o movie.mkv movie.avi english.srt
mkvmerge -o output.mkv input.mkv --language 0:ger --track-name 0:German subs.srt
```

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


### nmap
Scan for open SSH ports.
```bash
nmap -p22 --open -PN -sV -oG ssh_hosts 10.0.0.0/24
```

### openssl
Check the expiration date of an SSL certificate.
```bash
echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -dates
```

### ssh
Ghetto VPN
```bash
ssh -D 7070 remotehost
```
Then set your SOCKS proxy as localhost:7070

### watch
Alert with a beep when an address is unresponsive.
```bash
watch -b ping -c 1 -t 2 google.com
```

### wireshark
Decode non-standard port as HTTP and show fields
```
tshark port 4567 -d tcp.port==4567,http -T fields -e ip.src -e http.x_forwarded_for -e tcp.dstport -e http.user_agent -e http.request.uri
```
Dump full packet organized by fields in XML
```
tshark -f "dst port 4567" -d tcp.port==4567,http -T pdml
```