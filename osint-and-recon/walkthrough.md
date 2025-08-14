# SEC504 Ranges.io CTF – Walkthrough

This document contains my step-by-step solutions for the OSINT and web-based challenges.  
Each section includes the **objective**, **approach**, and **answer/flag**.

---

## Website Reconnaissance
**Question:** What is ISS Employee Jerald Orestes' job title?  

**Steps Taken:**
1. Navigated to [https://www.issplaylist.com/](https://www.issplaylist.com/).
2. Clicked **Company** in the navigation bar.
3. Searched the page with `Ctrl + F` → entered **Jerald**.
4. Found Jerald Orestes listed as an employee.

**Answer:**  
`Web Developer`

---

## Username Format Reconnaissance
**Question:** What convention does ISS Playlist use for username creation?  

**Steps Taken:**
1. On the **Company** page, hovered over employee names.
2. Observed that usernames appeared in the URL format.
3. Identified the naming convention.

**Answer:**  
`First Initial + Last Name` (e.g., jorestes)

---

## ClippedBin OSINT 1
**Question:** Examine the website contents at [www.clippedbin.com](http://www.clippedbin.com). Submit the flag.  

**Steps Taken:**
1. Opened the site → initially saw the “Create” page (no flag found).
2. Explored **Recent** and **Trending** tabs.
3. Tested search with keywords related to `issplaylist.com`.
4. Searched for `NetWars` (flag format is `NetWars{FLAG}`).
5. Opened the first link titled *Jackets Play Hands*.

**Flag:**  
`NetWars{ImportantOSINT}`

---

## ClippedBin OSINT 2
**Question:** Identify ISS Playlist password information disclosed by an attacker. A password is repeated 4 times in the list. Submit the password.  

**Steps Taken:**
1. Searched for `passwords` on ClippedBin.
2. Opened the only available link.
3. Found the repeated password.

**Answer:**  
`spacestation`

---

## IP Camera Logon
**Question:** Access the cam.issplaylist.com camera logon page. Submit the flag.  

**Steps Taken:**
1. Visited [cam.issplaylist.com](http://cam.issplaylist.com).
2. Observed the flag displayed in the bottom banner.

**Flag:**  
`NetWars{WebcamServer}`

---

## IP Camera Access
**Question:** Gain access to the IP camera system and submit the flag after logging in.  

**Steps Taken:**
1. Recognized this section was OSINT-focused.
2. Searched ClippedBin for `cam.issplaylist.com`.
3. Found leaked login credentials.  
   - **Username:** `admin`  
   - **Password:** `admin`
4. Logged in and retrieved the flag.

**Flag:**  
`NetWars{WebcamLoggedInAccess}`

---

## IP Camera Disclosure
**Question:** Who does Bridget need to follow up with?  

**Steps Taken:**
1. Used the camera controls to scan the office.
2. Located a clue written on the whiteboard.

**Answer:**  
`Phyliss`

---

