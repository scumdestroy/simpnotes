
##BANNERGRAB
```
nc IP PORT
telnet IP PORT
openssl s_client -connect 10.0.0.3:993
```
## NTLM INFO DISCLOSURE
```
telnet example.com 143
...
>> a1 AUTHENTICATE NTLM
+
>> TlRMTVNTUAABAAAAB4IIAAAAAAAAAAAAAAAAAAAAAAA=
+ TlRMTVNTUAACAAAACgAKADgAAAAFgooCBqqVKFrKPCMAAAAAAAAAAEgASABCAAAABgOAJQAAAA9JAEkAUwAwADEAAgAKAEkASQBTADAAMQABAAoASQBJAFMAMAAxAAQACgBJAEkAUwAwADEAAwAKAEkASQBTADAAMQAHAAgAHwMI0VPy1QEAAAAA
```
```
imap-ntlm-info NSE Script
nmap -p 143,993 --script imap-ntlm-info 10.0.0.3
```