# Penetration Testing Process
- Penetration Testing process overview
- Preparation for a penetration test
- Contracts and their components
- Handling meetings with clients
- Information Gathering
- Vulnerability Assessment
- Exploitation
- Post-Exploitation
- Lateral Movement
- Proof of Concept (PoC)
- Post-Engagement
- Practice & Continuous Improvement
---


## External vs Internal Penetration Testing

### External Penetration Test

The tester starts from outside the organization's network with no prior access.

**Objectives**
- Identify internet-facing assets.
- Discover vulnerabilities.
- Gain initial access.

**Examples**
- Web Applications
- VPN
- Mail Servers
- Public APIs

---

### Internal Penetration Test

The tester starts from inside the organization's network after gaining initial access or in an assumed breach scenario.

**Objectives**
- Privilege Escalation
- Lateral Movement
- Access Sensitive Systems

**Examples**
- Active Directory
- File Servers
- Domain Controllers

### Key Differences

| External | Internal |
|----------|----------|
| Starts outside the network | Starts inside the network |
| Gains initial access | Expands access inside the network |
| Targets internet-facing systems | Targets internal systems |

---

## Types of Penetration Testing

| Type | Description |
|------|-------------|
| **Black Box** | Minimal information is provided (e.g., IP address or domain only). |
| **Grey Box** | Partial information is provided (e.g., URLs, hostnames, subnets). |
| **White Box** | Full information is provided (e.g., source code, credentials, configurations). |
| **Red Team** | Simulates real-world attacks, including social engineering and physical security. |
| **Purple Team** | Attackers and defenders collaborate to improve security. |

---

## Common Testing Environments

A penetration test may target one or more of the following:

- Networks
- Web Applications
- APIs
- Mobile Applications
- Cloud
- IoT
- Active Directory
- Source Code
- Firewalls

---

## Penetration Testing Process

A penetration test follows a structured process. Each stage builds on the previous one, and the tester may return to earlier stages whenever additional information is needed.

1. Pre-Engagement
2. Information Gathering
3. Vulnerability Assessment
4. Exploitation
5. Post-Exploitation
6. Lateral Movement
7. Proof of Concept
---

## Penetration Testing Workflow

```text
Pre-Engagement
      ↓
Information Gathering
      ↓
Vulnerability Assessment
      ↓
Exploitation
      ↓
Post-Exploitation
      ↓
Lateral Movement
      ↓
Proof of Concept
      ↓
Post-Engagement
```

---
## Pre-Engagement

### Purpose

Prepare the penetration test before any technical testing begins.

---

### Main Activities

1. Scoping Questionnaire
2. Pre-Engagement Meeting
3. Contract / Scope of Work (SoW)
4. Rules of Engagement (RoE)
5. Kick-off Meeting
6. Environment Setup

---

### Key Documents

1. Non-Disclosure Agreement (NDA)
2. Scoping Questionnaire
3. Scoping Document
4. Penetration Testing Proposal / Scope of Work (SoW)
5. Rules of Engagement (RoE)
6. Contractors Agreement (Physical Assessments)
7. Reports

---

### Why It Matters

- Defines the testing scope.
- Ensures legal authorization.
- Sets client expectations.
- Reduces risk during the engagement.

---

### Key Terms

- **NDA** → Protects confidential information.
- **Scope** → Defines what can and cannot be tested.
- **RoE** → Rules that govern the penetration test.
- **SoW** → Defines the services, timeline, and deliverables.

---

### What I Learned

- A penetration test starts with planning, not hacking.
- Written authorization is required before testing.
- Seven key documents may be prepared during the engagement.
---
## Information Gathering

### Purpose

Collect as much information as possible about the target before attempting exploitation.

---

### Types of Information Gathering

#### 1. Open Source Intelligence (OSINT)

Gather publicly available information about the target.

**Examples**
- GitHub
- LinkedIn
- DNS
- Search Engines
- Job Posts

---

#### 2. Infrastructure Enumeration

Identify the target infrastructure.

**Examples**
- Domains
- Subdomains
- DNS
- Mail Servers
- Cloud Services
- Firewalls

---

#### 3. Service Enumeration

Identify services running on target hosts.

**Examples**
- Open Ports
- Service Versions
- Service Configurations

---

#### 4. Host Enumeration

Collect detailed information about individual hosts.

**Examples**
- Operating System
- Running Services
- Users
- Applications
- Configuration Files

---

### Pillaging

Collect sensitive information after gaining access to a target system.

**Examples**
- Passwords
- SSH Keys
- Tokens
- Configuration Files
- Sensitive Documents

---

## What I Learned

- Information Gathering is the foundation of every penetration test.
- Enumeration helps identify attack vectors before exploitation.
- Pillaging is performed after compromising a system.
---
## Vulnerability Assessment

### Purpose

Analyze the information gathered during the Information Gathering phase to identify potential vulnerabilities and attack vectors.

---

### Main Activities

- Analyze collected information
- Research known vulnerabilities (CVEs)
- Validate findings
- Identify attack vectors
- Prioritize potential targets

---

### Common Vulnerability Resources

- CVE
- NIST
- ExploitDB
- Vulners
- Packet Storm Security

---

### Key Concepts

- Do not trust assumptions without verification.
- Validate services manually when necessary.
- A vulnerability may require modifying the original PoC before exploitation.
- Information Gathering and Vulnerability Assessment are iterative processes.

---

## What I Learned

- Vulnerability Assessment focuses on analysis rather than exploitation.
- Enumeration results must be validated before launching attacks.
- If no attack vector is found, return to Information Gathering and collect more information.
- A real penetration test prioritizes quality over speed.
---
## Exploitation

### Purpose

Exploit identified vulnerabilities to gain initial access to the target system.

---

### Main Activities

- Select the best attack vector.
- Modify PoC if necessary.
- Test exploits safely.
- Gain initial access.
- Document every action.

---

### Attack Prioritization

When choosing an exploit, consider:

- Probability of Success
- Complexity
- Probability of Damage

---

### Best Practices

- Test exploits in a local lab whenever possible.
- Avoid attacks that may damage production systems.
- Communicate with the client before performing risky exploits.
- Keep detailed notes during exploitation.

---

## What I Learned

- Exploitation is more than simply running an exploit.
- A PoC often requires modification before use.
- Successful exploitation depends on selecting the safest and most reliable attack vector.
---
## Post-Exploitation

### Purpose

Perform additional actions after gaining initial access to maximize the value of the assessment.

---

### Main Activities

- Evasive Testing
- Local Information Gathering
- Pillaging
- Persistence
- Local Vulnerability Assessment
- Privilege Escalation
- Data Exfiltration

---

### Key Concepts

**Local Information Gathering**
- Enumerate local users
- Network configuration
- Running services
- Shares

**Pillaging**
- Search for credentials
- SSH keys
- Configuration files
- Sensitive documents
- Tokens

**Persistence**
Maintain access if the initial session is lost.

**Privilege Escalation**
Gain higher privileges such as root or SYSTEM.

**Data Exfiltration**
Demonstrate that sensitive data can be extracted safely (with client approval).

---

## What I Learned

- Post-Exploitation begins after obtaining initial access.
- Information Gathering starts again from the internal perspective.
- Pillaging helps identify valuable information and credentials.
- Privilege Escalation opens additional attack opportunities.
- Data Exfiltration demonstrates the business impact of a compromise.
---
## Lateral Movement

### Purpose

Move through the internal network after gaining initial access to assess the potential impact of a compromise.

---

### Main Activities

- Pivoting
- Internal Information Gathering
- Internal Vulnerability Assessment
- Privilege Exploitation
- Post-Exploitation

---

### Pivoting

Use the compromised host as a bridge to access internal systems that are not directly reachable.

---

### Common Objectives

- Access additional hosts
- Escalate privileges
- Reuse credentials
- Collect sensitive data
- Reach high-value assets

---

## What I Learned

- Lateral Movement begins after obtaining initial access.
- The compromised host can be used to reach internal systems through pivoting.
- The penetration testing process repeats from the internal perspective.
- Existing credentials and internal misconfigurations often enable further access.
---
## Proof of Concept (PoC)

### Purpose

Provide evidence that a vulnerability exists and demonstrate how it can be reproduced and validated.

---

### Common Forms of PoC

- Step-by-step reproduction
- Screenshots
- HTTP Requests / Responses
- Videos
- Scripts or exploit code

---

### Goals

- Help administrators reproduce the issue.
- Demonstrate the security impact.
- Validate remediation efforts.
- Support technical documentation.

---

### Key Concepts

- A PoC is not always an exploit.
- Fix the root cause, not just the exploit.
- Multiple vulnerabilities may form an attack chain.
- Remediation should eliminate the underlying weakness.

---

## What I Learned

- A PoC provides clear evidence that a vulnerability is exploitable.
- Documentation can be as valuable as exploit code.
- Security teams should address the underlying cause instead of blocking a single exploit.
- Documentation is required throughout the entire engagement.
