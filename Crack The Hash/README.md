<div align="center">

# 🔓 Crack The Hash
### cracking 9 hashes, from "throw it at an online tool" to "okay fine I'll open hashcat"

![Platform](https://img.shields.io/badge/Platform-TryHackMe-red?style=flat-square)
![Category](https://img.shields.io/badge/Category-Hash%20Cracking-blue?style=flat-square)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=flat-square)
![Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)

</div>

---

## So what's this room about

It's basically a pile of 9 hashes you have to identify and crack. The first few are the "nice" ones —
plain MD5/SHA1/SHA256, no salt, crack online in two seconds. The back half is where it actually gets
interesting: salted hashes, bcrypt, sha512crypt — stuff that online crackers either refuse to touch or
take forever on, so you end up pulling out hashcat and rockyou.txt for real.

## What I used

- `hashcat`
- `rockyou.txt`
- hashcat's example hashes wiki page (hashcat.net/wiki/doku.php?id=example_hashes) to double-check mode numbers

## How it went down

For each hash the process is: look at the length/format → figure out the algorithm → find the right
`-m` mode number → run it against rockyou.

```bash
# install hashcat if it's not already there
sudo apt update && sudo apt install hashcat -y

# grab the wordlist
curl -L -o rockyou.txt https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt
```

**#1 — MD5** (32 hex chars, plain, no prefix)
```bash
hashcat -m 0 -a 0 48bb6e862e54f2a795ffc4e541caed4d rockyou.txt
```

**#2 — SHA1** (40 hex chars)
```bash
hashcat -m 100 -a 0 CBFDAC6008F9CAB4083784CBD1874F76618D2A97 rockyou.txt
```

**#3 — SHA256** (64 hex chars)
```bash
hashcat -m 1400 -a 0 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032 rockyou.txt
```

**#4 — bcrypt** (`$2y$12$...` prefix, cost factor 12)
```bash
hashcat -m 3200 -a 0 '$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom' rockyou.txt
```
This one took noticeably longer than the rest — bcrypt is built to be slow on purpose, so every
candidate guess costs way more than it does with MD5/SHA1/SHA256.

**#5 — MD5** (32 hex chars)
```bash
hashcat -m 0 -a 0 279412f945939ba78ce0758d3fd83daa rockyou.txt
```

**#6 — SHA256** (64 hex chars)
```bash
hashcat -m 1400 -a 0 F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85 rockyou.txt
```

**#7 — MD5** (32 hex chars)
```bash
hashcat -m 0 -a 0 1DFECA0C002AE40B8619ECF94819CC1B rockyou.txt
```

**#8 — sha512crypt** (`$6$` prefix, salt baked right into the hash string)
```bash
hashcat -m 1800 -a 0 '$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.' rockyou.txt
```
Salt here is `aReallyHardSalt`, but you don't have to split it out — the whole `$6$salt$hash` string
goes to hashcat as-is.

**#9 — SHA1, salted separately** (salt given on its own: `tryhackme`)
```bash
echo 'e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme' > hash9.txt
hashcat -m 110 -a 0 hash9.txt rockyou.txt
```
This is the one that actually taught me something. Regular SHA1 mode (`-m 100`) won't crack it because
the salt isn't part of the hash string — it's appended separately. Mode `110` tells hashcat the format
is `sha1(password . salt)`, so you feed it `hash:salt` and it handles the rest.

## What I actually learned

- Hash length + prefix basically gives away the algorithm: 32 hex = MD5, 40 hex = SHA1, 64 hex = SHA256,
  and anything starting with `$2y$`, `$6$`, `$1$` etc. tells you the algorithm right in the string.
- Salt can either be baked into the hash (sha512crypt) or handed to you separately (the SHA1 one) —
  that changes the input format and which mode you pick.
- Online crackers are fine for the easy stuff, but the second you hit bcrypt/sha512crypt/salted hashes
  you need hashcat + a real wordlist running locally.
- bcrypt being deliberately slow to compute is exactly why it's the better pick for actually storing
  passwords in an application, vs MD5/SHA1 which are fast and therefore easy to brute-force.

## About the flag(s)

Per TryHackMe's rules, the cracked passwords aren't posted here in plain text. Each one above is
confirmed correct on the platform — crack it yourself with the commands if you want to verify.

---

<div align="center">

Writeup by **Simranjit Kaur** · part of my [CTF writeup collection](../README.md)

</div>
