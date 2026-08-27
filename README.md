![Status](https://img.shields.io/badge/Status-Completed-brightgreen) ![Module](https://img.shields.io/badge/Module-W3--PM1-blue) ![Module](https://img.shields.io/badge/Module-W3--PM2-blue) ![Scope](https://img.shields.io/badge/Scope-Authorized%20Only-orange) ![Methods](https://img.shields.io/badge/Methods-2%2F2%20Completed-9cf) ![Flags](https://img.shields.io/badge/Flags-2%2F2%20Captured-brightgreen) ![Tools Used](https://img.shields.io/badge/Tools%20Used-6-lightgrey)

### W3-PM2 | CYBERSECURITY | NETWORKWALKS ACADEMY

# Week 3 – Cracking a Password Protected PDF (John the Ripper + Networkwalks Tools)

## What this lab was about

For Week 3 of my Networkwalks Cybersecurity Internship, the task was to take a password protected PDF, pull a crackable hash out of it, and recover the original password using a dictionary attack. I ended up doing this two different ways, on two separate copies of the sample file: once the "traditional" way with John the Ripper through its Johnny GUI, and once using Networkwalks' own browser based hash calculator and cracker. Both routes got me to a working password and both unlocked a copy of the PDF to reveal a completion flag, so I've documented each path separately below since the tools, the file copy, and the steps weren't identical.

> This was carried out entirely inside a controlled internship lab environment on files that were provided for this exercise. Nothing here should be pointed at a file, account, or system you don't have explicit permission to test.

## Goals for the exercise

- Understand what a "password hash" actually is when we're talking about an encrypted PDF, and why you can't just read the password straight out of the file
- Extract that hash using two different extraction tools and compare the results
- Run an actual dictionary attack against the hash rather than just reading about how one works
- Confirm the recovered password actually opens the file (not just that the tool says "cracked")
- Get comfortable with John the Ripper's GUI front end (Johnny), since most of what I'd seen before was command line only
- Write the whole workflow up in a way I could hand to someone else and have them reproduce it

## Kit used

| Tool | What I used it for |
|---|---|
| OnlineHashCrack – PDF Hash Extractor | Converting the locked PDF into a `pdf2john` style hash |
| Johnny (GUI for John the Ripper) | Loading the hash and running the dictionary attack locally |
| John the Ripper 1.9.0-jumbo-1 | The actual cracking engine behind Johnny |
| Networkwalks Hash Calculator | A second, browser based way to pull the same kind of hash straight from the PDF |
| Networkwalks Password Cracker | An in-browser dictionary attack tool that mimics how John the Ripper works |
| Microsoft Edge / Microsoft Word | Viewing the locked PDF and holding the raw hash text before pasting it into Johnny |

## Route 1: John the Ripper via Johnny

### Step 1 – Pulling the hash out of the PDF

The PDF itself doesn't store the password in plain text anywhere, so before John the Ripper (or anything else) can attempt to guess it, you need something in a format the cracking tool understands. I used the online PDF Hash Extractor tool for this: uploaded `My Locked PDF1.pdf`, and it ran `pdf2john` behind the scenes to spit out a hash string starting with `$pdf$4*4*128*-1028*1*16*...`.

![blank PDF Hash Extractor page](onlinehash.png)

![PDF file selected and ready to upload](uploadfileonlinehash.png)

Once it finished processing, the tool returned the full hash in a text box on the page, which I selected and copied.

![copying the generated hash](copyhash.png)

### Step 2 – Getting the hash somewhere Johnny could read it

Johnny expects a file it can open, so I pasted the copied hash into a document and saved it, then dropped that into the working folder for this task alongside the rest of the lab files.

![hash file placed in the working folder ready for Johnny](saveintoafolder.png)

### Step 3 – Pointing Johnny at the John the Ripper executable

Before Johnny can run anything, it needs to know where the actual `john.exe` binary lives on disk. Under Settings I set the path to the jumbo build I'd downloaded and Johnny confirmed it had detected John the Ripper 1.9.0-jumbo-1.

![setting the John the Ripper executable path inside Johnny](setpathinjohnny.png)

### Step 4 – Loading the hash and kicking off the attack

From there it was a case of opening the password file I'd saved earlier through Johnny's "Open password file" option, confirming it picked up the hash correctly and starting a new attack session.

![opening the saved hash file inside Johnny](openpasswordfile.png)

![hash pasted into Word before saving](penhash1.png)

![starting the attack in Johnny](startnewattack.png)

### Step 5 – Result

It didn't take long for Johnny to come back with a match. The status bar read `100% (1/1: 1 cracked, 0 left) [format=PDF]` and the recovered password showed up as **`good-luck`**.

![Johnny reporting the password has been cracked](password.png)


### Step 6 – Confirming it actually works

A cracked password on screen doesn't mean much until you've actually used it, so I opened this copy of the PDF and typed in `good-luck`. The file unlocked straight away and revealed the first flag for this exercise:

```
nw{cybersecurity_flag_captured_2608}
```


![PDF unlocked with the password recovered by John the Ripper](openfile.png)

![Johnny reporting the password has been cracked](crackpassword.png)

## Route 2: Networkwalks' own browser based tools

Wanting to see how this same process looks without installing anything, I repeated the exercise using Networkwalks' own lab tools, which walk through the same idea (hash extraction, then a dictionary attack) but entirely inside the browser. This time I worked from a fresh, separate copy of the sample file downloaded straight from the lab page, so the hash and password recovered here are independent of Route 1's result.

### Step 1 – Grabbing the sample file from the lab page

The lab task page provided the same locked PDF as a download, so I grabbed a fresh copy from there to work with.

![downloading the locked PDF from the Networkwalks lab page](downloadfile.png)

### Step 2 – Extracting the hash with the Hash Calculator

The Hash Calculator tool has a dedicated PDF tab that reads the file locally in the browser (nothing gets uploaded to a server) and pulls out the same style of `$pdf$` hash that `pdf2john` produces.

![Hash Calculator homepage before uploading](hashcalculaor.png)

![uploading this copy of the PDF into the Networkwalks Hash Calculator](uploadfile.png)

![the Hash Calculator generating the crackable PDF hash](pm2_copyhash.png)

The tool confirmed the file was encrypted and handed back the hash along with some useful metadata: revision R4, version V4, 128-bit key length.

### Step 3 – Feeding the hash into the Password Cracker

The Password Cracker page is built around the same logic John the Ripper uses: hash every candidate word from a list and compare it against the target hash until something matches. I pasted the hash from the calculator straight into the input box.

![Password Cracker page before pasting the hash](openpasswordcracker.png)

![pasting the extracted hash into the Password Cracker tool](pm2_pastethehash.png)

By default the tool comes with a built-in list of 100 common passwords, but I also tried swapping in a proper wordlist to see the option in action.

![loading a custom wordlist into the cracker](wordlist.png)

### Step 4 – Watching the attack run

Once I hit start, the tool worked through the list in real time, skipping comment lines from the wordlist header and testing each real candidate against the hash, until it landed on a match.

```
[+] MATCH password1 ✓
```

![Password Cracker successfully matching the hash](pm2_crackpassword.png)

The recovered password this time was **`password1`**, different from the one John the Ripper found, since this was a separate copy of the sample file with its own hash and password set for the browser based lab.

### Step 5 – Unlocking the file and grabbing the second flag

Same as before, the real test was opening the PDF with the recovered password to make sure it wasn't a false positive.

![entering the recovered password into this copy's PDF prompt](openfile&enterpassword.png)

It opened without issue and revealed the second flag:

```
nw{networkwalks_flag1_jtr_270521_1}
```

![second flag revealed after unlocking the file](finalcrack.png)

## What actually stuck with me

Running through this twice, once with a proper offline tool and once entirely in a browser, made a few things click that I don't think I'd have picked up just reading about password cracking:

- A "hash" isn't the password scrambled up, it's a one-way fingerprint. The only way to "reverse" it is to keep guessing inputs and hashing them until something produces a matching fingerprint. That's the entire idea behind a dictionary attack.
- Both `good-luck` and `password1` cracked almost instantly because they're exactly the kind of thing that shows up near the top of any common password list. Neither tool had to work hard.
- The extraction step matters just as much as the cracking step. If the hash isn't pulled out correctly (wrong format, missing salt, wrong revision) the cracking tool has nothing useful to work with, regardless of how good your wordlist is.
- Doing the same task with a GUI tool like Johnny versus a purpose built web app made it obvious that the underlying mechanics don't change, just how much of the process is hidden from you. Johnny still expects you to configure the John the Ripper binary path and manage the attack session yourself; the web tool handles all of that for you.

## Why this matters from a defensive angle

Both passwords fell in seconds using nothing more than a small dictionary. That's the whole point of the exercise: it's a very direct demonstration of why short, common, or predictable passwords offer almost no real protection once someone has the hash. A few practical takeaways worth carrying forward:

- Avoid anything that would appear on a top-100 or top-1000 password list, "password1" being a textbook example
- Length and unpredictability matter far more than complexity rules like forcing a symbol or a capital letter
- Where possible, protected files and accounts should also be backed by a second factor of authentication, so that even a cracked password isn't enough on its own
- If you're responsible for distributing sensitive documents, consider that PDF encryption alone is not a strong control against a motivated attacker with the right tools

## Internship details

**Program:** Networkwalks Cybersecurity Internship<br>
**Week:** 3 <br>
**Task:** Password recovery from an encrypted PDF using John the Ripper and Networkwalks' cracking tools <br>
**Focus area:** Applied cybersecurity / ethical hacking fundamentals <br>

## A note on ethics

Everything in this write-up was carried out against sample files supplied specifically for the internship task, inside a lab environment set up for that purpose. Password cracking techniques like the ones shown here should only ever be used against systems, files, or accounts you own or have explicit written permission to test. Using them against anything else is illegal in most jurisdictions and not something this repository is meant to encourage.

## 👤 Author

**Muhammad Sami Ullah** Cybersecurity Professional | B082 <br>
🔗 LinkedIn: [linkedin.com/in/muhammad-sami-ullah-it](https://www.linkedin.com/in/muhammad-sami-ullah-it/) <br>
**Project Information:** Program: Cybersecurity Program at Networkwalks | Week: 03
