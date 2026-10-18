<div align="center">

# 🏴‍☠️ Simple CTF

### a beginner TryHackMe box that chains a CMS SQLi, a weak SSH password, and a classic vim privesc

![Platform](https://img.shields.io/badge/Platform-TryHackMe-red?style=flat-square&logo=tryhackme&logoColor=white)
![Category](https://img.shields.io/badge/Category-Web%20Exploitation%20%2F%20Privesc-blueviolet?style=flat-square)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=flat-square)
![Flag](https://img.shields.io/badge/Flag-redacted%20🙈-lightgrey?style=flat-square)

</div>

---

## 👋 So what's this room about

**Simple CTF** is TryHackMe's go-to "first real box" room — it's not flashy, but it quietly makes you touch almost every stage of a pentest in one go: port scanning, web enumeration, exploiting a known CVE, cracking a password, and privilege escalation. The target's running an old install of **CMS Made Simple**, and the whole chain from "nmap scan" to "root shell" takes maybe 20–30 minutes once you know where to look.

> ⚠️ Per usual CTF-etiquette, I'm **not** posting the password, the user flag, or the root flag here. Screenshots below have all of that blacked out.

---

## 🧰 What I used

- Kali Linux (attack box)
- `nmap`
- `ffuf` (directory brute-forcing)
- `hydra` (SSH password brute-forcing)
- `ssh`
- a bit of Google / Exploit-DB for the CVE

---

## 🪜 How it went down

### 1. nmap scan to see what's actually open

Started simple — ran an nmap version scan against the target IP to check what ports and services were live.

```bash
nmap -sV 10.49.167.216
```

```
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
```

![nmap scan results](images/01-nmap.png)

Two services under port 1000 (ftp, http), and ssh tucked away on the non-standard 2222.

### 2. Enumerating the web server

Port 80 didn't show much on the surface — just a default-looking Apache page. Ran `ffuf` against it to brute force for hidden directories instead of trusting what was linked on the page.

```bash
ffuf -u http://10.49.181.129/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

![ffuf directory brute-force](images/02-ffuf.png)

That turned up `/simple` — and visiting it landed on a **CMS Made Simple** install. The version number was sitting right in the footer:

![CMS Made Simple version in the footer](images/03-cms-version.png)

**CMS Made Simple 2.2.8.**

### 3. Finding the CVE (and a free username)

Searched for known exploits against that exact version and found **CVE-2019-9053** — a SQL injection in CMS Made Simple that lets you dump the admin's username and password hash straight from the login flow.

While poking around the site itself, a news post also leaked a username in the "posted by" field — no injection needed for that one, it was just sitting in plain sight:

![username leaking from a news post](images/04-username-leak.png)

So between the SQLi CVE and the site itself, I had a valid username to work with.

### 4. Brute-forcing SSH with hydra

With a username in hand, I pointed `hydra` at SSH with a common password wordlist. Took a couple of wrong port guesses (it's running on 2222, not 22) before it actually connected.

```bash
hydra -l <username> -P /usr/share/seclists/Passwords/Common-Credentials/best110.txt ssh://10.49.181.129:2222 -t 4
```

![hydra brute-force — password blacked out](images/05-hydra-redacted.png)

Got a hit. One valid password found.

### 5. Logging in and grabbing the user flag

SSH'd in with the cracked creds, grabbed `user.txt`, and had a quick look at `/home` to see who else was on the box.

![ssh login, user flag redacted, and another user sitting in /home](images/06-ssh-login-redacted.png)

There was a second user — **sunbath** — sitting right there in `/home`, though I never needed to pivot into that account for this room.

### 6. Privilege escalation via vim

Ran `sudo -l` to see what the current user was allowed to run as root, and found this:

```
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim
```

![sudo -l showing passwordless vim](images/07-sudo-l.png)

Passwordless `vim` as root is a textbook privesc — vim can drop you straight into a shell:

```bash
sudo vim -c ':!/bin/sh'
```

That popped a root shell. From there it was just a matter of finding and reading the root flag.

![root shell and root flag, redacted](images/08-root-redacted.png)

Box rooted. 🎉

---

## 🧠 What I actually learned from this one

- **Check the footer / "powered by" text before reaching for an exploit DB search.** Half the battle on CMS boxes is just finding the exact version — and a lot of CMSes print it for free if you look.
- **Usernames leak from the weirdest places.** No injection, no brute force — just a news post with a "posted by" field nobody thought to scrub before the box was built.
- **Always double check the actual port before brute-forcing.** Wasted a couple of hydra runs hitting port 22 before realizing ssh was on 2222 — the nmap output had the answer the whole time.
- **`sudo -l` should be one of the first things you run after getting a shell.** Misconfigured sudo rules (especially on editors like vim, less, find) are one of the most common and fastest privesc paths on beginner boxes — GTFOBins has a huge list of these if you ever get stuck.

---

## 🤐 About the flags

Not posting the password, user flag, or root flag — all three are blacked out in the screenshots above on purpose. If this room's on your list, everything above gets you to the door; the actual values are one command away once you follow the same steps.

---

<div align="center">

🏳️ *Writeup by [Simranjit Kaur](#) — part of a running [CTF writeup collection](../README.md)*

</div>