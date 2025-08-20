# SEC504 Ranges.io CTF – Walkthrough

This document contains my step-by-step solutions for the OSINT and web-based challenges.  
Each section includes the **objective**, **approach**, and **answer/flag**.

---

## Website Reconnaissance
**Question:** What is ISS Employee Jerald Orestes' job title?  

**Steps Taken:**
I navigated to [https://www.issplaylist.com/](https://www.issplaylist.com/) and clicked on the **Company** tab in the navigation bar. Once there, I used `Ctrl + F` and searched for the name **Jerald**. That quickly brought me to Jerald Orestes in the employee list, where I saw his job title displayed.  

<details>
  <summary>Click to reveal the answer</summary>

  `Web Developer`

</details>

---

## Username Format Reconnaissance
**Question:** What convention does ISS Playlist use for username creation?  

**Steps Taken:**
While still on the **Company** page, I hovered my mouse over several employee names. I noticed that each name revealed a pattern in the URLs, which contained usernames. By checking a few examples, I figured out the convention they used: first initial of the first name + full last name. For Jerald Orestes, that became `jorestes`.  

<details>
  <summary>Click to reveal the answer</summary>

  `First Initial + Last Name` (e.g., jorestes)

</details>

---

## ClippedBin OSINT 1
**Question:** Examine the website contents at [www.clippedbin.com](http://www.clippedbin.com). Submit the flag.  

**Steps Taken:**
When I opened ClippedBin, I first landed on a “Create” page with no obvious flag. I decided to explore other sections and found **Recent** and **Trending**, both of which had a search bar. Knowing that the flag format was `NetWars{FLAG}`, I tried searching for terms connected to ISS Playlist, like `NetWars` itself. This search led me to a link titled *Jackets Play Hands*. Opening it revealed the flag I needed.  

<details>
  <summary>Click to reveal the flag</summary>

  `NetWars{ImportantOSINT}`

</details>

---

## ClippedBin OSINT 2
**Question:** Identify ISS Playlist password information disclosed by an attacker. A password is repeated 4 times in the list. Submit the password.  

**Steps Taken:**
I used the ClippedBin search again, this time with the keyword `passwords`. That led me to a leaked list. Looking through it carefully, I noticed one particular password repeated four times. That repetition made it stand out as the one the question was asking for.  

<details>
  <summary>Click to reveal the answer</summary>

  `spacestation`

</details>

---

## IP Camera Logon
**Question:** Access the cam.issplaylist.com camera logon page. Submit the flag.  

**Steps Taken:**
I visited [cam.issplaylist.com](http://cam.issplaylist.com) directly. The login page displayed a banner across the bottom that contained the flag. No login was required for this part.  

<details>
  <summary>Click to reveal the flag</summary>

  `NetWars{WebcamServer}`

</details>

---

## IP Camera Access
**Question:** Gain access to the IP camera system and submit the flag after logging in.  

**Steps Taken:**
Knowing this section was OSINT-related, I searched ClippedBin again, this time for `cam.issplaylist.com`. In the results, I found leaked credentials with the username and password both set as `admin`. I entered those on the camera login page, successfully logged in, and retrieved the flag.  

- **Username:** `admin`  
- **Password:** `admin`  

<details>
  <summary>Click to reveal the flag</summary>

  `NetWars{WebcamLoggedInAccess}`

</details>

---

## IP Camera Disclosure
**Question:** Who does Bridget need to follow up with?  

**Steps Taken:**
Once inside the camera system, I experimented with the controls to move the camera around the office. While scanning the environment, I spotted writing on a whiteboard. It said that Bridget needed to follow up with a specific person, and that gave me the answer.  

<details>
  <summary>Click to reveal the answer</summary>

  `Phyliss`

</details>

---
