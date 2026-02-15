## Task 1 : Which is the highest open TCP port on the target machine?

Run an nmap scan on the target

```
sudo nmap -sV -Pn -T4 -oA initial_scan 10.129.229.26
```

![[Pasted image 20260216024608.png]]

>Answer : 55555

---

## Task 2 : What is the name of the open source software that the application on 55555 is "powered by"?

Visit the web address

```
http://10.129.229.26:55555
```

![[Pasted image 20260216024950.png]]

>Answer : request-baskets

---

## Task 3 : What is the version of request-baskets running on Sau?


![[Pasted image 20260216025120.png]]

> Answer : 1.2.1

---

## Task 4 : What is the 2023 CVE ID for a Server-Side Request Forgery (SSRF) in this version of request-baskets?

A google search of "request-baskets version 1.2.1 CVE" returns the result. 

I visited this [website](https://www.incibe.es/en/incibe-cert/early-warning/vulnerabilities/cve-2023-27163)

![[Pasted image 20260216025505.png]]

> Answer : CVE-2023-27163

---

## Task 5 : What is the name of the software that the application running on port 80 is "powered by"?



>Answer : Maltrail

