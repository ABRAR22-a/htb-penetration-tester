# Getting Started

## Module Objectives

* An overview of Information Security
* Penetration testing distros
* Common terms and technologies
* Scanning and enumeration basics
* Using public exploits
* Shells, privilege escalation, and transferring files
* Navigating the HTB platform
* A step-by-step walkthrough of a retired HTB box
* Common pitfalls and asking questions effectively
* Completing a box without a walkthrough

---

# Basic Tools

## SSH

### Purpose

* Secure remote access to Linux systems.
* More stable than a reverse shell.
* Can transfer files.
* Can be used for pivoting.

### Common Commands

```bash
ssh user@IP
scp file user@IP:/path
```

---

## Netcat (nc)

### Purpose

* Connect to TCP/UDP ports.
* Banner Grabbing.
* Listen for Reverse Shells.
* Transfer files.

### Banner

A banner is a message sent by a service when a client connects to it. It may reveal:

* Service name
* Software version
* Operating system

### Banner Grabbing

Banner Grabbing is the process of collecting banner information to identify running services and software versions.

### Example

Banner Grabbing

```bash
nc IP 22
```

Example Output

```text
SSH-2.0-OpenSSH_8.4
```

Reverse Shell Listener

```bash
nc -lvnp 4444
```

### Options

| Option | Description   |
| ------ | ------------- |
| `-l`   | Listen        |
| `-v`   | Verbose       |
| `-n`   | No DNS Lookup |
| `-p`   | Port          |

---

## Socat

### Purpose

* Advanced version of Netcat.
* Better reverse shells.
* Port Forwarding.
* Tunneling.
* Upgrade shells to an interactive TTY.

---

## tmux

### Purpose

Manage multiple terminal sessions inside one terminal.

### Useful During Pentesting

* Run Nmap while using Burp Suite.
* Keep reverse shells open.
* SSH into multiple machines.

### Useful Shortcuts

Start tmux

```bash
tmux
```

New Window

```text
Ctrl + B then C
```

Split Vertically

```text
Ctrl + B then %
```

Split Horizontally

```text
Ctrl + B then "
```

---

## Vim

### Purpose

Edit text files directly on Linux systems.

### Basic Commands

Insert Mode

```text
i
```

Exit Insert Mode

```text
Esc
```

Save

```text
:w
```

Quit

```text
:q
```

Save & Quit

```text
:wq
```

Quit Without Saving

```text
:q!
```

### Useful Shortcuts

* `dd` Delete line
* `yy` Copy line
* `dw` Delete word
* `p` Paste

---

# Commands Learned

```bash
ssh user@IP

scp file user@IP:/path

nc IP PORT

nc -lvnp 4444

tmux

vim filename
```

---

# Lab

## Banner Grabbing

```bash
nc 154.57.164.82 30364
```

### Output

```text
SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.1
```

### What I Identified

* Service: SSH
* Software: OpenSSH
* Version: 8.2p1
* Operating System: Ubuntu

---

## What I Learned

* SSH provides stable remote access to Linux systems.
* Netcat can identify services using Banner Grabbing.
* Socat provides more advanced networking capabilities than Netcat.
* tmux helps manage multiple terminal sessions efficiently.
* Vim is an essential text editor for Linux and remote systems.
* Documenting commands and lab results makes future review much easier.



- `yy` Copy line
- `p` Paste
- `dw` Delete word
---
# Service Enumeration

## Goal

Identify running services before attempting exploitation.

Always remember:

> Enumeration comes before Exploitation.

---

## What is a Service?

A service is a program listening on a network port.

Examples

| Port | Service |
|------|----------|
| 21 | FTP |
| 22 | SSH |
| 80 | HTTP |
| 445 | SMB |
| 161 | SNMP |

---

# Nmap

## Purpose

Network Mapper used to discover hosts, ports, services, and versions.

---

## Basic Scan

```bash
nmap IP
```

Scans the 1000 most common TCP ports.

---

## Scan All Ports

```bash
nmap -p- IP
```

Scans every TCP port (1–65535).

---

## Version Detection

```bash
nmap -sV IP
```

Detects software versions.

Example

```text
OpenSSH 8.2p1
Apache 2.4.41
vsFTPd 3.0.3
```

---

## Default Scripts

```bash
nmap -sC IP
```

Runs Nmap Default NSE Scripts.

Examples

- FTP Anonymous Login
- HTTP Title
- SMB Information

---

## Recommended Scan

```bash
nmap -sC -sV -p- IP
```

This is one of the most common commands used during HTB and penetration tests.

---

# Nmap Scripting Engine (NSE)

NSE scripts automate service enumeration.

Scripts are stored in:

```bash
/usr/share/nmap/scripts/
```

---

## Run a Specific Script

```bash
nmap --script script-name IP
```

Examples

Banner

```bash
nmap --script banner IP
```

FTP Anonymous

```bash
nmap --script ftp-anon IP
```

SMB OS Discovery

```bash
nmap --script smb-os-discovery IP
```

---

# FTP Enumeration

## Default Port

```
21/TCP
```

---

## Purpose

Transfer files between systems.

---

## Connect

```bash
ftp IP
```

---

## Anonymous Login

Username

```
anonymous
```

Password

```
Press Enter
```

or

```
anonymous
```

---

## Useful Commands

List Files

```bash
ls
```

Current Directory

```bash
pwd
```

Change Directory

```bash
cd folder
```

Download File

```bash
get filename
```

Upload File

```bash
put filename
```

Exit

```bash
exit
```

---

## Look For

- passwords.txt
- backup.zip
- config.php
- id_rsa
- SSH Keys
- Database Backups
- Credentials

---

## Enumeration Workflow

```
FTP Found
↓

Try Anonymous Login
↓

Browse Files
↓

Download Interesting Files
↓

Extract Credentials
↓

Use Credentials Elsewhere
```

---

# SMB Enumeration

## Default Port

```
445/TCP
```

Sometimes

```
139/TCP
```

---

## Purpose

Windows File Sharing.

---

## Shares

A Share is a shared folder available over the network.

Examples

- Users
- HR
- Finance
- Backups

---

## List Shares

```bash
smbclient -L //IP
```

---

## Anonymous Access

```bash
smbclient //IP/Share -N
```

---

## Login as User

```bash
smbclient //IP/Share -U username
```

---

## Useful Commands

List Files

```bash
ls
```

Current Directory

```bash
pwd
```

Change Directory

```bash
cd folder
```

Download

```bash
get file.txt
```

Upload

```bash
put file.txt
```

Exit

```bash
exit
```

---

## Useful NSE Scripts

```bash
nmap --script smb-os-discovery IP
```

```bash
nmap --script smb-enum-shares IP
```

```bash
nmap --script smb-enum-users IP
```

---

## Look For

- Credentials
- Password Files
- Backup Files
- SSH Keys
- Database Dumps
- Sensitive Documents

---

## Enumeration Workflow

```
SMB Found
↓

List Shares
↓

Anonymous Access?
↓

Browse Files
↓

Download Interesting Files
↓

Extract Credentials
↓

Reuse Credentials
```

---

# SNMP Enumeration

## Default Port

```
161/UDP
```

---

## Purpose

Used by administrators to monitor and manage network devices.

May reveal:

- Hostname
- Users
- Running Processes
- Installed Software
- Network Interfaces
- System Information

---

## Scan UDP Port

```bash
nmap -sU -p 161 IP
```

---

## Community Strings

Similar to a password.

Common values

```
public
```

```
private
```

---

## snmpwalk

```bash
snmpwalk -v2c -c public IP
```

Options

| Option | Meaning |
|---------|----------|
| -v2c | SNMP Version |
| -c | Community String |

---

## Discover Community Strings

```bash
onesixtyone -c community.txt IP
```

---

## Enumeration Workflow

```
SNMP Found
↓

Try public
↓

Collect System Information
↓

Extract Valuable Information
↓

Use Information During Later Stages
```

---

# Common Enumeration Workflow

```
Run Nmap
↓

Identify Open Ports
↓

Detect Versions

↓

Run NSE Scripts

↓

Enumerate Each Service

↓

Collect Credentials

↓

Identify Attack Vectors

↓

Proceed to Exploitation
```

---

# Quick Reference

| Service | Port | First Step |
|----------|------|------------|
| FTP | 21/TCP | Try Anonymous Login |
| SSH | 22/TCP | Banner Grabbing / Credentials |
| HTTP | 80/443 | Browse Website & Enumerate |
| SMB | 445/TCP | Enumerate Shares |
| SNMP | 161/UDP | Try public using snmpwalk |
---

# Practice

## Exercise 1

### Question

Perform an Nmap scan of the target.

What does Nmap display as the version of the service running on port **8080**?

### Command

```bash
nmap -A -p8080 10.129.47.22
```

### Explanation

| Option | Meaning |
|---------|----------|
| `-A` | Aggressive Scan (OS Detection, Version Detection, Default NSE Scripts, Traceroute) |
| `-p8080` | Scan only port 8080 |

### Output

```text
PORT     STATE SERVICE VERSION
8080/tcp open  http    Apache Tomcat
```

### Answer

```text
Apache Tomcat
```

### What I Learned

- `-A` performs an aggressive scan.
- Nmap detected the service version as **Apache Tomcat**.
- Port **8080** is commonly used for web applications such as **Apache Tomcat**.
---

## Exercise 2

### Question

Perform an Nmap scan of the target and identify the non-default port that the **Telnet** service is running on.

### Command

```bash
nmap -sC -sV 10.129.47.22
```

### Explanation

| Option | Meaning |
|---------|----------|
| `-sC` | Run the default NSE scripts |
| `-sV` | Detect service versions |

### Output

```text
PORT     STATE SERVICE      VERSION

445/tcp  open  netbios-ssn  Samba smbd 4.x
2323/tcp open  telnet       Linux telnetd
```

### Answer

```text
2323
```

### What I Learned

- `-sV` detects the service version.
- Telnet normally runs on **port 23**.
- During enumeration, never assume services use their default ports.
- Nmap identified that **Linux telnetd** was running on the non-default port **2323**.
---

## Exercise 3

### Question

List the SMB shares available on the target host.

Connect to the available share as the **bob** user.

Once connected, access the folder called **flag** and submit the contents of the **flag.txt** file.

---

### Step 1 - List SMB Shares

```bash
smbclient -L //10.129.47.22 -N
```

### Explanation

| Option | Meaning |
|---------|----------|
| `-L` | List available SMB shares |
| `//IP` | Target host |
| `-N` | No password (Anonymous login) |

### Output

```text
Sharename
---------
print$
users
IPC$
```

---

### Step 2 - Connect to the Share

```bash
smbclient //10.129.47.22/users -U bob
```

### Explanation

| Option | Meaning |
|---------|----------|
| `//IP/users` | Connect to the users share |
| `-U bob` | Authenticate as the user bob |

---

### Step 3 - Browse the Share

List files

```bash
ls
```

Enter the flag directory

```bash
cd flag
```

List files again

```bash
ls
```

---

### Step 4 - Download the Flag

```bash
get flag.txt
```

Exit SMB

```bash
exit
```

---

### Step 5 - Read the Flag

```bash
cat flag.txt
```

---

### Answer

```text
dceece590f3284c3866305eb2473d099
```

---
# Web Enumeration

## Goal

Collect as much information as possible about a web application before attempting exploitation.

> Enumeration comes before Exploitation.

---

# Web Enumeration Workflow

```
Open Website
↓

View Source Code

↓

Check robots.txt

↓

Inspect HTTP Headers

↓

Fingerprint Technologies

↓

Directory Enumeration

↓

Subdomain Enumeration

↓

Search for Vulnerabilities
```

---

# Gobuster

## Purpose

Directory and File Brute Forcing.

Used to discover hidden:

- Directories
- Files
- Admin Panels
- Backups
- Upload Folders

---

## Directory Enumeration

```bash
gobuster dir -u http://IP -w /usr/share/seclists/Discovery/Web-Content/common.txt
```

### Options

| Option | Meaning |
|---------|----------|
| `dir` | Directory enumeration mode |
| `-u` | Target URL |
| `-w` | Wordlist |

---

## Example Output

```text
/index.php     200
/admin         403
/wordpress     301
```

---

# HTTP Status Codes

| Code | Meaning |
|------|----------|
| 200 | OK (Resource exists) |
| 301 | Permanent Redirect |
| 302 | Temporary Redirect |
| 403 | Forbidden (Exists but no access) |
| 404 | Not Found |
| 500 | Internal Server Error |

---

# DNS Subdomain Enumeration

## Purpose

Discover hidden subdomains.

Examples

- admin.example.com
- vpn.example.com
- api.example.com
- blog.example.com

---

## Command

```bash
gobuster dns -d example.com -w /usr/share/seclists/Discovery/DNS/namelist.txt
```

### Options

| Option | Meaning |
|---------|----------|
| `dns` | DNS enumeration mode |
| `-d` | Domain |
| `-w` | Wordlist |

---

# cURL

## Purpose

Retrieve HTTP response headers.

Useful for Banner Grabbing.

---

## Command

```bash
curl -IL http://IP
```

### Options

| Option | Meaning |
|---------|----------|
| `-I` | Show HTTP headers only |
| `-L` | Follow redirects |

---

## Information You May Find

- Web Server
- Server Version
- PHP Version
- Framework
- Authentication Methods

---

# WhatWeb

## Purpose

Fingerprint web technologies automatically.

Detects:

- Web Server
- CMS
- Programming Language
- Framework
- JavaScript Libraries

---

## Command

```bash
whatweb IP
```

---

## Example Output

```text
Apache 2.4.41
PHP 7.4
WordPress
Bootstrap
```

---

# SSL/TLS Certificates

HTTPS websites may reveal useful information inside their certificates.

Possible Information

- Company Name
- Email Address
- Organization
- Subdomains

---

# robots.txt

## Purpose

Instruct search engines which pages should not be indexed.

---

## Check

```text
http://IP/robots.txt
```

---

## Example

```text
Disallow: /admin
Disallow: /private
Disallow: /uploads
```

Hidden directories found here should always be investigated.

---

# Source Code

Always inspect the HTML source.

Shortcut

```
Ctrl + U
```

---

## Look For

- Comments
- Hidden Links
- Test Credentials
- API Keys
- JavaScript Files
- Sensitive Information

Example

```html
<!--
username=test
password=test123
-->
```

---

# Common Web Enumeration Checklist

- Open the website
- Check the page source
- Read robots.txt
- Inspect HTTP headers
- Run WhatWeb
- Run Gobuster
- Enumerate subdomains
- Identify technologies
- Search for vulnerabilities

---

# Common Commands

```bash
gobuster dir -u http://IP -w WORDLIST
```

```bash
gobuster dns -d DOMAIN -w WORDLIST
```

```bash
curl -IL http://IP
```

```bash
whatweb IP
```

---

# What I Learned

- Web Enumeration is one of the most important phases of a penetration test.
- Hidden directories often expose sensitive functionality.
- HTTP headers reveal valuable information about the target.
- WhatWeb quickly fingerprints web technologies.
- robots.txt and Source Code frequently expose useful information.
- Always enumerate thoroughly before attempting exploitation.

### What I Learned

- Use `smbclient -L` to enumerate SMB shares.
- Anonymous access (`-N`) may reveal available shares.
- Use `-U` to authenticate as a specific user.
- SMB provides commands similar to Linux such as `ls` and `cd`.
- `get` downloads files from the remote SMB share to the local machine.
- After downloading a file, use Linux commands such as `cat` to read its contents.
