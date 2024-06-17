# Maquina-Trust 
## Resolviendo maquina-Trust de "El Pingüino de Mario" 

> Paso N°:1 Scaneo con nmap. (Fase de reconocimiento)

```bash
nmap -A 172.17.0.2
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-17 09:40 -03
Nmap scan report for 172.17.0.2
Host is up (0.000094s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT STATE SERVICE VERSION
22/tcp open ssh OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
| ssh-hostkey:
| 256 19:a1:1a:42:fa:3a:9d:9a:0f:ea:91:7f:7e:db:a3
(ECDSA)
|_ 256 a6:fd:cf:45:a6:95:05:2c:58:10:73:8d:39:57:2b
(ED25519)
80/tcp open http Apache httpd 2.4.57 ((Debian))
|_http-server-header: Apache/2.4.57 (Debian)
|_http-title: Apache2 Debian Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.49 seconds
```

## Paso N°2: Enumeracion usando gobuster.

```bash
gobuster dir -u "http://172.17.0.2" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt,py,bak,php.bak
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://172.17.0.2
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php,txt,py,bak,php.bak
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 275]
/secret.php           (Status: 200) [Size: 927]
/.php                 (Status: 403) [Size: 275]
/server-status        (Status: 403) [Size: 275]
Progress: 1323360 / 1323366 (100.00%)
===============================================================
Finished
===============================================================
```
## Paso N°3: revisar y utilizar lo encontrado.

### _En éste caso vemos un "/secret.php" el cual vamos a usar poniendolo en la web como "http://172.17.0.2/secret.php" ,en la salida nos encontramos con un mensaje que dice: 
> mario, ésta pagina web no es vulnerable.
<img src="/home/eze/Pictures/maquinakali/scaneo4.png" alt="Scaneo" width="400">


Asi que como nos da un nombre (Mario) podemos usarlo de prueba para ver si podemos obtener acceso por medio del ssh,
pero como no tenemos la contraseña vamos a usar ataque de fuerza bruta en éste caso con hydra.











