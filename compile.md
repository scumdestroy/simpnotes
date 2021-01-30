
## Compile 32bit binary
`gcc rdsexploit.c -m32 -o rds`

install 32 bit compiler libraries if Failures occur
`apt-get install gcc-multilib`

## Use this alternative command if you get a lirbaray missing or outdated issue on the target box
`gcc -m32 -Wl,--hash-style=both 9542.c -o 9542`

## Compile 32bit program for Windows
`i686-w64-mingw32-gcc 121.c -lws2_32 -o 121_exploit.exe`
OR
`x86_64-w64-mingw32-gcc shell.c -o shell.exe`

## Compile 32bit C++ program
`i686-w64-mingw32-g++ 11650.c -lws2_32 -o exploit`

## Compile 64bit program for Windows
`i686-w64-mingw32-gcc shell.c -o shell.exe`


# Nix Compile Errors

- Bad Register Name Errors
    bad register name issues have meant wrong arch use "-m64" if using "-m32" in gcc command

- bad ELF interpreter: No such file or directory
    also bad arch but have only seen if I try and execute binary compiled with -m32 on an x86_64 machine compile without the -m32 because my machine will auto use x64

- ld not found
    "'ld'" not found by gcc error then run gcc from the usr/bin dir ie;
    gcc -B /usr/bin ptrace-kmod.c -o p

- undeclared (first use in this function) Errors
    these appear if you didn't "define" the function OR didn't include an #include library
    not sure why but some exploit in this openfuck.c made this happen

ie:
`764.c:1149:16: error: ‘SSL2_MT_SERVER_VERIFY’ undeclared (first use in this function); did you mean ‘SSL3_MT_SERVER_HELLO’?`

Fix:
```
#define SSL2_MT_SERVER_VERIFY 5
/usr/lib/gcc/i686-linux-gnu/7/../../../i386-linux-gnu/Scrt1.o: In function `_start':
    use -nostartfiles option
    gcc exp_moosecox.c -o pipe -nostartfiles
```

- Finding Undeclared strings in their include files
ie:
`clone((int (*)(void *))trigger,(void *)((unsigned long)newstack + 65536),CLONE_VM | CLONE_CHILD_CLEARTID | SIGCHLD,&fildes, NULL, NULL, target);`

Search in /usr/src for the string
    find /usr/src -type f | xargs grep -i CLONE_CHILD_CLEARTID
Result:
`/usr/src/linux-headers-4.12.0-kali2-common/include/uapi/linux/sched.h:#define CLONE_CHILD_CLEARTID`
Add the discovered library to your exploit code

- version `GLIBC_2.7' not found
    https://thomask.sdf.org/blog/2018/07/13/compiling-for-old-glibc-fscanf.html
    compiles fins on kali but will spit this erorr on victim machine
    this error comesu p because of an old glibc library being used by the victim machine
    compile with the old extension naming schemes
    gcc -m32 14814.c -o 14814 -D_GNU_SOURCE

- ORIG_RAX UNDEFINED
    basically can't use -m32 switch since it looks as the code may need x64 compile only...

- Page Size Undeclared
    https://stackoverflow.com/questions/37897645/page-size-undeclared-c

- Input this header
`#include <sys/user.h>`
fatal error: bits/c++config.h
    install g++ libraries
    https://stackoverflow.com/questions/4643197/missing-include-bits-cconfig-h-when-cross-compiling-64-bit-program-on-32-bit

- 8478.sh: line 89: $'\r': command not found
    happens due to the file being windows delimite written etc ie; https://askubuntu.com/questions/966488/how-do-i-fix-r-command-not-found-errors-running-bash-scripts-in-wsl
    Fix with this tool
    dos2unix 8478.sh

- error while loading shared libraries: requires glibc 2.5 or later dynamic linker
    needs the "Wl,--hash-style=both"
    gcc -m32 -D_GNU_SOURCE -Wl,--hash-style=both -o linux-sendpage 9545.c

- Compiling Headers when required by exploits
    ie; https://www.exploit-db.com/exploits/42887
    basically this one requires you build a header file first as noticed by the cat into file commands
    copy this section into a script ie;(/root/oscp/lab-net2019/hosts/leftturn/make.sh)
    next you can compile the rest of the exploit normally as it will have this header file built by the script using cat and EOF switches for multiline cats within a script

- Floating Point Exception
    basically compile on the host itself

- Windows Cross/Compile Errors on Kali
    fwrite/fread issue: Add "#include <unistd.h>" in the binary and recompile. https://stackoverflow.com/questions/16178876/c-implicit-declaration-of-function
    #include <Windows.h> Fatal error means change to lowercase "windows.h"
    use locate to find missing files on our system to include by either fixing the casing ie "Windows.h" is "windows.h" in our libraries or otherwise include from anohther exploit into the current directory so gcc can find it and compile with it
    locate -i "Windows.h"
    fprint not declared issue: need to include the proper module/header file
    #include <stdio.h>
    .sln files need special visual studio cmd prompt to compile

- Missing DLLs
    Do a locate on the missing dlls and copy them into the cwd and try running them with wine again. This should get rid of teh import errors and loading errors.
    if you see it loaded and still had an error you need to import the rigth arch used ie 32bit dll not 64 etc

### EXAMPLE:
```
python27) root@kali:~/oscp/lab-net2019/hosts/jeff# wine exploit.exe 0009:err:module:import_dll Library libgcc_s_sjlj-1.dll (which is needed by L"Z:\root\oscp\lab-net2019\hosts\jeff\exploit.exe") not found
0009:err:module:import_dll Loading library libstdc++-6.dll (which is needed by L"Z:\root\oscp\lab-net2019\hosts\jeff\exploit.exe") failed (error c000007b).
0009:err:module:attach_dlls Importing dlls for L"Z:\root\oscp\lab-net2019\hosts\jeff\exploit.exe" failed, status c0000135
```
# Other Exploit errors

- Permission Denied when running binary
    this can be because of the directory your using and may not allow execution find a notehr world writeable dir and try it there ie; /dev/shm location has worked

- Syntax error: end of file unexpected (expecting "then")
    something is wrong upon uploading the script us vi <filename> and copy paste it into a file on the target and save it and run it'll work if you can do this better off
