# Hack The Box Starting Point - Full Report

This report summarizes my learning process and findings during the completion of the HTB Starting Point Tier 0 machines: **Dancing**, **Redeemer**, and **Appointment**.

---

##  General Enumeration Process

Before diving into each machine, I followed this general approach:

1. **Ping the target**: Check if it's alive.
2. **Nmap scan**: Identify open ports, services, and versions.
3. **Enumeration of services**: Use relevant tools for HTTP, FTP, SMB, SNMP, etc.
4. **Look for vulnerabilities**: Based on service and version info.
5. **Exploit**: Use either known exploits or manual testing.
6. **Capture the flag**: Usually in a `flag.txt` file on the system.

---

##  Dancing

### IP: `10.129.95.131`

### Nmap Scan:
```
nmap -sC -sV -oN dancing.nmap 10.129.95.131
```

- Found **FTP (21)** and **HTTP (80)** open.
- FTP allowed anonymous login.

### FTP Enumeration:
```
ftp 10.129.95.131
Name: anonymous
Password: anonymous
```
- Retrieved `flag.txt` from the FTP root.

### Lessons Learned:
- How to perform FTP enumeration.
- Anonymous login can expose sensitive data.

---

## 💾 Redeemer

### IP: `10.129.95.147`

### Nmap Scan:
```
nmap -sC -sV -oN redeemer.nmap 10.129.95.147
```

- Found port **6379** open (Redis database).

### Redis Enumeration:
- Connected using `redis-cli`:
```
redis-cli -h 10.129.95.147
```
- Used commands like `INFO`, `KEYS *`, `GET`, `SET` to interact with data.
- Found the flag with:
```
KEYS *
GET flag
```

### Lessons Learned:
- Basic use of Redis commands.
- Redis exposed without auth can leak sensitive data.

---

##  Appointment

### IP: `10.129.211.165`

### Nmap Scan:
```
nmap -sC -sV -oN appointment.nmap 10.129.211.165
```

- Found port **80 (HTTP)** open.

### Web Enumeration:
- Used `gobuster` to brute force directories:
```
gobuster dir -u http://10.129.211.165 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

- Found `/login.php`

### SQL Injection:
- Input fields vulnerable to SQLi.
- Used `' OR 1=1-- -` in the username field to bypass login.
- First word on page: `Welcome`

### Lessons Learned:
- Manual SQL injection to bypass login.
- Gobuster usage and flags.

---

##  Tools Summary

| Tool       | Purpose                                 | Usage Example |
|------------|-----------------------------------------|---------------|
| `nmap`     | Port scanning & version detection       | `nmap -sC -sV -oN scan.nmap IP` |
| `ftp`      | Access FTP servers                      | `ftp IP` |
| `redis-cli`| Interact with Redis database            | `redis-cli -h IP` |
| `gobuster` | Directory brute-forcing on webservers   | `gobuster dir -u URL -w wordlist` |

---
- Always check for anonymous FTP access.
- Redis without authentication is a goldmine.
- Basic SQLi is still dangerous when input is not sanitized.
- Enumeration is 80% of the work. Exploitation comes after.
---

**End of Report**
