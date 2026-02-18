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
