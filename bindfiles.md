%SYSTEMDRIVE%\boot.ini A file that can be counted on to be on virtually every windows host. Helps with confirmation that a read is happening. WARNING - in more recent versions of Windows this file in no longer there.
%WINDIR%\win.ini 	This is another file that can be counted on to be readable by all users of a system.
%SYSTEMROOT%\repair\SAM
%SYSTEMROOT%\System32\config\RegBack\SAM 	Stores user passwords in either an LM hash and/or an NTLM hash format. The SAM file in \repair is locked, but can be retrieved using forensic or Volume Shadow copy methods.
%SYSTEMROOT%\repair\system
%SYSTEMROOT%\System32\config\RegBack\system 	This is the SYSTEM registry hive. This file is needed to extract the user account password hashes from a Windows system. The SYSTEM file in \repair is locked, but can be retrieved using forensic or Volume Shadow copy methods.
%SYSTEMROOT%\repair\SAM
%SYSTEMROOT%\System32\config\RegBack\SAM 	These files store the LM and NTLM hashes for local users. Using Volume Shadow Copy or Ninja Copy you can retrieve these files.
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software
%WINDIR%\repair\security 	System registry hives. https://en.wikipedia.org/wiki/Windows_Registry
%SYSTEMROOT%\repair\SAM
%SYSTEMROOT%\System32\config\SAM
%SYSTEMROOT%\System32\config\RegBack\SAM 	Stores user passwords in either an LM hash and/or an NTLM hash format. The SAM file in \repair is locked, but can be retrieved using forensic or Volume Shadow copy methods.
%SYSTEMROOT%\repair\SYSTEM
%SYSTEMROOT%\System32\config\SYSTEM
%SYSTEMROOT%\System32\config\RegBack\SYSTEM 	This is the SYSTEM registry hive. This file is needed to extract the user account password hashes from a Windows system. The SYSTEM file in \repair is locked, but can be retrieved using forensic or Volume Shadow copy methods.
%SYSTEMDRIVE%\autoexec.bat 	autoexec.bat is a startup script that executes at startup. As Webopedia states, “Stands for automatically executed batch file, the file that DOS automatically executes when a computer boots up. This is a convenient place to put commands you always want to execute at the beginning of a computing session. For example, you can set system parameters such as the date and time, and install memory-resident programs.”
%SYSTEMDRIVE%\pagefile.sys 	This file is used by the operating system when there is not enough RAM (memory) in the system. It is a large file, but contains spill over from RAM, usually lots of good information can be pulled, but should be a last resort due to size.
%SystemDrive%\inetpub\logs\LogFiles 	IIS 7.x web server log file location.
%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat 	Internet Explorer web browser history file (http://support.microsoft.com/kb/322916)
%USERPROFILE%\ntuser.dat 	User-level Windows registry settings (http://technet.microsoft.com/en-us/library/cc758618(v=WS.10).aspx)
%WINDIR%\System32\drivers\etc\hosts 	System hosts file for local translation of host names to IP addresses.
%WINDIR%\debug\NetSetup.log 	Shows issues when computers are joined to a domain. http://technet.microsoft.com/en-us/library/cc961817.aspx
%WINDIR%\iis[version].log where [version] = 6, 7, or 8 	Internet Information Service (IIS web server) log files.
%WINDIR%\system32\CCM\logs\*.log 	Windows SCCM (System Center Configuration Manager) log files (http://technet.microsoft.com/en-us/library/bb892800.aspx)
%WINDIR%\system32\config\AppEvent.Evt
%WINDIR%\system32\config\SecEvent.Evt 	Windows Event Logs.
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav 	Backup Windows registry files (http://forensics.wikia.com/wiki/Windows_registry_entries)
%WINDIR%\system32\logfiles\httperr\httperr1.log 	IIS 6.x web server error logs.
%WINDIR%\system32\logfiles\w3svc1\exYYMMDD.log where YYMMDD = year month day 	Web server log files.
unattend.txt, unattend.xml, unattended.xml, sysprep.inf 	Used in the automated deployment of Windows images and can contain user accounts. Sometimes found in the %WINDIR%\Panther\ directory.
