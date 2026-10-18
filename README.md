<div align="center">

# 🚩 CTF Writeups

### A running log of rooms, boxes, and bugs — by Simranjit Kaur

![TryHackMe](https://img.shields.io/badge/TryHackMe-red?style=flat-square&logo=tryhackme&logoColor=white)
![Web Exploitation](https://img.shields.io/badge/Focus-Web%20%26%20AppSec-blueviolet?style=flat-square)
![Writeups](https://img.shields.io/badge/Writeups-1-informational?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)

</div>

---

Notes, screenshots, and full walkthroughs from CTF rooms and labs I've worked through — mostly web exploitation and application security, written up the way I'd want to read them back in six months.


## 📚 Index

| Room | Platform | Category | Difficulty | Vulnerability | Writeup |
|---|---|---|---|---|---|
| 💌 TryHeartMe | TryHackMe | Web Exploitation | 🟢 Easy | Unverified/Forgeable JWT (CWE-347) | [Read →](TryHeartMe/README.md) |
| 🎯 TakeOver | TryHackMe | Recon / Enumeration | 🟡 Medium | Hostname disclosure via TLS cert SAN field | [Read →](TakeOver/README.md) |

> New writeups get added as a new folder + a new row here.

## 🗂️ Repo Structure

```text
TryHackMe-writeups/
├── README.md                  ← you are here
├── TryHeartMe/
│   ├── README.md               ← full writeup
│   └── images/                 ← numbered screenshots referenced in the writeup
└── TakeOver/
    ├── README.md               ← full writeup
    └── images/                  
```

## 🧭 How each writeup is organized

Every room folder follows the same shape so they're easy to skim:

- **TL;DR** — the exploit in a few lines, for anyone just browsing
- **Room info** — platform, category, difficulty, vulnerability class
- **Methodology** — numbered steps with collapsible screenshots
- **Root cause / Impact / Remediation** — the "why it matters" section
- **Key takeaway** — one line worth remembering

## ⚠️ A note on scope

These writeups cover publicly released, intentionally vulnerable training rooms/labs. No live production systems, real credentials, or third-party targets are included.

---

<div align="center">

⭐ If something here is useful, a star helps more people find it.

</div>

