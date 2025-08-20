# SEC504 Ranges.io CTF - Walkthrough

This document contains my personal step-by-step solutions for the **store.issplaylist.com** section challenges.  
Each section includes **question**, **steps taken**, and **answer/flag**.

---

## Not for Sale Category
**Question:** Which item is not for sale on the ISS Playlist store site?

**Steps Taken:** Visiting [store.issplaylist.com](https://store.issplaylist.com/) showed a variety of merchandise on the homepage. Looking closely, each set of merchandise is separated by category. Comparing the choices from the question to the categories available on the homepage revealed the answer.

<details>
  <summary>Click to reveal the answer</summary>

  `Sporting Goods`

</details>

---

## Classic Shirt Image
**Question:** Explore the store.issplaylist.com website. What is the file name of the full-size image for the ISS Playlist classic shirt?

**Steps Taken:** While on the website, I clicked the `Classic Shirt` item, right-clicked on the image, and selected **Open Image in New Tab**. The URL endpoint displayed the file name directly.

<details>
  <summary>Click to reveal the answer</summary>

  `00001-Full.jpg`

</details>

---

## Tote Bag Image
**Question:** Explore the store.issplaylist.com website. What is the file name of the full-size image for the ISS Playlist tote bag?

**Steps Taken:** Following the same steps as above, I clicked the `Tote Bag` item, opened the image in a new tab, and noted the filename from the URL.

<details>
  <summary>Click to reveal the answer</summary>

  `00011-Full.jpg`

</details>

---

## Sneaker Image
**Question:** What is the file name of the full-size image for the ISS Playlist sneaker?

**Steps Taken:** The sneaker image wasn’t listed on the website, so I inferred the naming convention from previous examples (`00001-Full.jpg`, `00011-Full.jpg`). Each image appeared to follow the format: **five digits + -Full.jpg**. Instead of guessing all 99,999 possibilities, I generated a number list using:

```bash
seq -w 00000 99999 > numbers.txt
```

Then I used `ffuf` to fuzz image paths:

```bash
ffuf -w numbers.txt -u https://store.issplaylist.com/static/images/FUZZ-Full.jpg -s -mc 200,301
```

This quickly revealed 16 valid images. Browsing through them, I identified the sneaker image.

<details>
  <summary>Click to reveal the answer</summary>

  `00014-Full.jpg`

</details>

---

## Dog Clothes
**Question:** Identify the ISS Playlist dog clothing article. Submit the flag.

**Steps Taken:** From the same image list generated earlier, I located the dog clothing picture. The flag was embedded directly in the image.

<details>
  <summary>Click to reveal the flag</summary>

  `NetWars{DogHoodie}`

</details>

---

## Completed Order
**Question:** Complete an order on the store.issplaylist.com website. Submit the flag displayed in the completed order page.

**Steps Taken:** Initially, I tried adding items to the cart, filling random checkout information, and pressing **Complete Order**, but nothing happened. The banner stated *“We are currently not accepting payments at the moment.”* I nearly gave up, but then I decided to inspect the HTML source.  

The checkout button’s script revealed that it would only work if the cart total was **$0** and all form fields were filled. I remembered that the **Stellar Soundwaves PDF** cost $0. I added it to the cart, filled the form with placeholder text (using `asd@email.com` for the email), and submitted. This successfully processed the order, and the receipt displayed the flag!

<details>
  <summary>Click to reveal the flag</summary>

  `NetWars{YouHaveReceipts}`

</details>

---

## Receipt Enumeration
**Question:** Exploit the order receipt feature of the website. Submit the 4-digit PIN used to unlock the customer lock box disclosed in an order receipt.

**Steps Taken:** The receipt URL followed the format `receipt-XXXXX`. I reused my `numbers.txt` list with ffuf:

```bash
ffuf -w numbers.txt -u https://store.issplaylist.com/receipt-FUZZ -s
```

This found 14 valid receipts. Manually browsing through them revealed the PIN.

<details>
  <summary>Click to reveal the answer</summary>

  `0523`

</details>

---

## Stellar Soundwaves Collectible
**Question:** Stellar Soundwaves is a collector's card from ISS Playlist. What genre of music is the focus of the first public collector's card?

**Steps Taken:** All of the receipts contained a link to a collectible card. Opening the link displayed the card image, which clearly listed the genre.

<details>
  <summary>Click to reveal the answer</summary>

  `Classical`

</details>

---

## URL Tampering
**Question:** Manipulate the URL used for the Stellar Soundwaves collector's card. Submit the flag.

**Steps Taken:** The card URL looked like this:  

```
https://store.issplaylist.com/stellarsoundwaves/ss-Y2xhc3NpY2Fs
```

By simply deleting the last character, the page revealed the hidden flag.

<details>
  <summary>Click to reveal the flag</summary>

  `NetWars{Base64Encoding}`

</details>

---

## Music Genre List
**Steps Taken:** Using **ClippedBin**, I searched for the keyword `genre`. This led me to a post name *Whips Cough Sisters* containing a list of music genres.

<details>
  <summary>Click to reveal the answer</summary>

  `antifolk`

</details>

---

## Base64 Genre Enumeration
**Question:**  

**Steps Taken:** From the previous URL tampering challenge, I noticed the string `Y2xhc3NpY2Fs` was base64 for `classical`. This gave me the idea to brute-force other valid cards by encoding genre names into base64.  

I created a text file with genres (`genres.txt`), using the genre list from the previous challenge, and converted them into base64 with a one-liner shell script:

```bash
while IFS= read -r line; do echo -n "$line" | base64; done < genres.txt > base64-genres.txt
```

Alternatively, I could have written a Python script, but the one-liner was faster.  

Finally, I fuzzed the card endpoints with ffuf:

```bash
ffuf -w base64-genres.txt -u https://store.issplaylist.com/stellarsoundwaves/ss-FUZZ -s
```

This revealed multiple valid collector’s cards. Checking them in Firefox eventually led to the final flag.

<details>
  <summary>Click to reveal the flag</summary>

  `NetWars{PunkRock}`

</details>

---

