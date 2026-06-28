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
