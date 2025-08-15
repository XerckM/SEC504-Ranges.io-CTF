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

After carefully re-reading the exploit script, I noticed that it specifically mentioned needing a **`.action` endpoint**. Sure enough, in the commented area of the exploit there was an example URL:  

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
At this point I knew the process: use the exploit to remotely execute commands and carefully enumerate files in the target directory. I ran another listing of `/opt/tomcat/webapps/ROOT/` to identify which file contained the password data, with the goal of reading it in the same way I retrieved the flag file. This step was about persistence and methodically checking each file for clues, just like I had done before.  

---

