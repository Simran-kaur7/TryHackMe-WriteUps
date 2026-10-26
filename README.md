<div align="center">

# 🚩 CTF Writeups

### A running log of rooms, boxes, and bugs — by Simranjit Kaur

![TryHackMe](https://img.shields.io/badge/TryHackMe-red?style=flat-square&logo=tryhackme&logoColor=white)
![Web Exploitation](https://img.shields.io/badge/Focus-Web%20%26%20AppSec-blueviolet?style=flat-square)
![Writeups](https://img.shields.io/badge/Writeups-7-informational?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)

</div>

---

Notes, screenshots, and full walkthroughs from CTF rooms and labs I've worked through — mostly web exploitation and application security, written up the way I'd want to read them back in six months.
 
## 📖 Index
 
| Room | Platform | Category | Difficulty | Vulnerability | Writeup |
|---|---|---|---|---|---|
| 💌 TryHeartMe | TryHackMe | Web Exploitation | 🟢 Easy | Unverified/Forgeable JWT (CWE-347) | [Read →](TryHeartMe/README.md) |
| 🎯 TakeOver | TryHackMe | Recon / Enumeration | 🟡 Medium | Hostname disclosure via TLS cert SAN field | [Read →](TakeOver/README.md) |
| 🏴‍☠️ Simple CTF | TryHackMe | Web Exploitation / Privesc | 🟢 Easy | SQL Injection (CVE-2019-9053) + Sudo Misconfiguration | [Read →](SimpleCTF/README.md) |
| 💬 Speed Chatting | TryHackMe | Web Exploitation | 🟢 Easy | Unrestricted File Upload → Remote Code Execution | [Read →](Speed%20Chatting/README.md) |
| 🔓 Crack The Hash | TryHackMe | Hash Cracking | 🟢 Easy | Weak/crackable password hashes (MD5, SHA1, SHA256, bcrypt, sha512crypt) | [Read →](Crack%20The%20Hash/README.md) |
| 💡 LightRoom | TryHackMe | SQL Injection / SQLite | 🟢 Easy | UNION-based SQL Injection (CWE-89) — raw TCP lookup service, keyword & comment-sequence filter bypass | [Read →](LightRoom/README.md) |
| 🥚 Easy Peasy | TryHackMe | Enumeration / Privilege Escalation | 🟢 Easy | Writable root cron job (CWE-732) | [Read →](Easy%20Peasy/README.md) |
 
---

> New writeups get added as a new folder + a new row here.

## 🗂️ Repo Structure

```text
TryHackMe-writeups/
├── README.md                  ← you are here
├── TryHeartMe/
│   ├── README.md               
│   └── images/                 
├── TakeOver/
│   ├── README.md               
│   └── images/
├── SimpleCTF/
│   ├── README.md               
│   └── images/
├── Speed Chatting/
│   ├── README.md               
│   └── images/
├── Crack The Hash/
│   └── README.md
├── LightRoom/
│   └── README.md
└── Easy Peasy/
    ├── README.md
    └── images/
```

## 🧭 How each writeup is organized

Every room folder follows the same shape so they're easy to skim:

- **So what's this room about** — the room in plain terms, what it's actually testing
- **What I used** — the tools that mattered for that specific room
- **How it went down** — numbered steps, with inline screenshots as proof
- **What I actually learned** — the takeaways worth remembering next time
- **About the flag(s)** — flags, passwords, and cracked hashes, redacted per TryHackMe's rules (never posted in plaintext)

## ⚠️ A note on scope

These writeups cover publicly released, intentionally vulnerable training rooms/labs. No live production systems, real credentials, or third-party targets are included.

---

<div align="center">

⭐ If something here is useful, a star helps more people find it.

</div>