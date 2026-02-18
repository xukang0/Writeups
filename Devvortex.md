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
joomla -u http://dev.devvortex.htb
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


