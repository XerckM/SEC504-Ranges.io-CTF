# SEC504 Ranges.io CTF - Walkthrough

This document contains my step-by-step solutions for the **orders.issplaylist.com** section challenges. 
Each section includes **question**, **steps taken**, and **answer/flag**.

---

## Search Engine Restriction
**Question:** Retrieve the search engine restriction resource on the web server orders.issplaylist.com. Enter the flag.

**Steps Taken:**
1. The question is tricky, but the key to answering this is the `search engine restriction` portion of the question. To find this restriction and retrieve the flag, it means we need to check the `robots.txt` for the website.
2. To achieve this we need to type in the address bar `https://orders.issplaylist.com/robots.txt` to see the flag.

**Flag:**
`NetWars{ValuableReconData}`

--

## Apache Struts Version
**Question:** Using the information on the orders.issplaylist.com website, identify the version of Apache Struts in use.

**Steps Taken:**
1. We can see this version clearly in the `robots.txt` file if we look at `Disallow: struts2-showcase-2.3.14` section.

**Answer:**
`2.3.14`

--
