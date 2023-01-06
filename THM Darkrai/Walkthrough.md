# DaRkrAi

## Introduction

This would be a beginner level CTF where players would face FTP login, SSH bruteforcing, Simple HTTP Enumerations, Some Googling, Steganography and finally Privilege Escalation. It would a really fun box for those who are starting their CTF missions.


## Writeup

### Ports
```bash
nmap -A -T4 -vv 10.10.5.5
```

![nmap](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221223231611.png)

![Pasted image 20221223231656.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221223231656.png)

#### **Key Processes**

SSH, FTP and HTTP Open. We shall look for a shared Directory in FTP. Then lets look at the Webpage to see if there is any info there, if we get a Static/Default webpage, we shall try Directory Busting, find some hidden directories. The fact that SSH is open, somehow there may be like some Credentials hidden somewhere. Bruteforcing the SSH without knowing both the username and password will be the last resort. Lets See how things pan out.

##### Automation / Crons

N/A

##### Firewall Rules

N/A

##### Docker

N/A

_________________________________________________________________________________________________________

### FTP

![Pasted image 20221224015243.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224015243.png)

we get the file there is something inside this file.

![Pasted image 20221224015419.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224015419.png)

So we need a passphrase.


### Apache

Nothing much, lets see the source code.

![Pasted image 20221223231903.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221223231903.png)

![Pasted image 20221223232032.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221223232032.png)

This can be a hint, Something that about the user being a hard Pokemon fan. Lets keep that iin mind.
Since there is nothing here lets try Directory Bruteforcing for any hidden directories.

```bash
gobuster dir -u http://10.10.5.5 -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt -t 64
```

![Pasted image 20221224014602.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224014602.png)

Lets check out this /move directory

![Pasted image 20221224014714.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224014714.png)

Another static page, lets look at the page source for any additional information.

![Pasted image 20221224014805.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224014805.png)

Got this at the end .... another hidden directory, lets see what it has to offer.

![Pasted image 20221224014857.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224014857.png)

As the iniitial hint all these revolves around the anime Pokemon and its characters, again lets see the page source.

![Pasted image 20221224015002.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224015002.png)

The strongest move of Darkrai is Dream Eater, Googling surely helps. Its a 2 word move, as the above hint states. so the passphrase should be "dreameater" 

### Steghide

```bash
steghide extract -sf cynthiya.jpeg
```

![Pasted image 20221224015541.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224015541.png)

We get the username, now that we have looked at the other 2 services, and got only a username, it might be a ssh bruteforce that lies ahead, lets do that

```bash
hydra -l renu -P /usr/share/wordlists/rockyou.txt 10.10.5.5 ssh -V
```

![Pasted image 20221224015733.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224015733.png)

Yup it was .... Login in via SSH

![Pasted image 20221224015836.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224015836.png)

Lets look for the flags.

![Pasted image 20221224020013.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224020013.png)

We should elevate to lily, So lets try to see if there is any files thats interesting, something with secret in it, and in the mean time go ahead and search the directories for any interesting files

![Pasted image 20221224020249.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224020249.png)

Lets view the contents of that file

![Pasted image 20221224020906.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224020906.png)

and this has to the base64 code of the password

![Pasted image 20221224021014.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224021014.png)

and we are in ..... now for the priv esc

![Pasted image 20221224021131.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224021131.png)
![Pasted image 20221224021150.png](https://github.com/darkrai069/THM_Machines/blob/main/THM%20Darkrai/Access/Pasted%20image%2020221224021150.png)

and thats it ......
